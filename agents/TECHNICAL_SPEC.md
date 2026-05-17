# Synthesis — Technical Specification
**Version 1.0 — May 2026**
**Status: Pre-development. This is the authoritative technical reference.**

---

## Table of Contents

1. [Product Overview](#1-product-overview)
2. [Tech Stack](#2-tech-stack)
3. [Repository Structure](#3-repository-structure)
4. [Environment Variables](#4-environment-variables)
5. [Database Schema (Supabase / PostgreSQL)](#5-database-schema)
6. [Row-Level Security (RLS)](#6-row-level-security)
7. [Authentication & Account Creation Flow](#7-authentication--account-creation-flow)
8. [Stripe Integration](#8-stripe-integration)
9. [n8n Automation Workflows](#9-n8n-automation-workflows)
10. [AI Agent Architecture](#10-ai-agent-architecture)
11. [File Generation & Storage](#11-file-generation--storage)
12. [Email System](#12-email-system)
13. [Dashboard — Frontend Routes & Access Control](#13-dashboard--frontend-routes--access-control)
14. [Ticker Swap Logic](#14-ticker-swap-logic)
15. [Earnings Detection & Auto-Update Flow](#15-earnings-detection--auto-update-flow)
16. [Admin Panel](#16-admin-panel)
17. [Deployment](#17-deployment)
18. [Development Phases](#18-development-phases)

---

## 1. Product Overview

Synthesis is a B2C SaaS platform that generates institutional-quality investment theses using a multi-agent AI system. Users pay per report (one-shot) or subscribe for continuous coverage of a portfolio of tickers.

**Target market:** USA — English only. All UI, reports, and emails are in English. A `/es` route for Spanish-speaking markets is planned for Phase 2, contingent on US validation.

### Product Tiers

#### One-Shot Reports
| Product | Price | Deliverable |
|---|---|---|
| Core | $9.99 | PDF (fundamental analysis) |
| Prime | $14.99 | PDF (fundamental + technical) |
| Edge | $29.99 | PDF + PPTX (full thesis + DCF) |

#### One-Shot Add-ons (shown post-purchase, before Stripe checkout)
| Add-on | Price | Description |
|---|---|---|
| News Pulse | +$4.99 | News alerts for the ticker — 90 days |
| Quarterly Refresh | +$14.99 | News Pulse + 1 updated analysis after next earnings |
| Yearly Sync | +$34.99 | News Pulse + analysis update after each of 4 quarterly earnings |

> Add-on analysis updates match the tier of the original purchase (Core → Core update, Edge → Edge update).

#### Subscriptions
| Plan | Active Tickers | Monthly | Annual | On-demand/month | Extra analysis |
|---|---|---|---|---|---|
| Core | 5 | $79 | $790 | +2 | $14.99 each |
| Prime | 10 | $149 | $1,490 | +5 | $14.99 each |
| Edge | Unlimited | $249 | $2,490 | +20 | $14.99 each |

All subscription plans include: full thesis (PDF + PPTX) on ticker activation, automatic update on every earnings report, and dashboard access.

---

## 2. Tech Stack

| Layer | Tool | Notes |
|---|---|---|
| Frontend | Next.js 14 (App Router) | Deployed on Vercel |
| Backend API | Next.js API Routes | Co-located with frontend |
| Database | Supabase (PostgreSQL 15) | Managed, includes Auth + Storage |
| Authentication | Supabase Auth | Email/password. No OAuth in v1. |
| Payments | Stripe | Checkout (one-shot) + Billing (subscriptions) |
| Automation | n8n Cloud | Orchestrates AI agent pipelines |
| AI Models | Anthropic Claude (Opus 4.7, Sonnet 4.6, Haiku 4.5) | See AI model routing table below. DeepSeek discarded due to data inconsistency on SEC filings. |
| Financial Data | Financial Modeling Prep (FMP) | Fundamentals, ratios, earnings calendar |
| Web Search | Tavily API | Research Agent — news and web context |
| PDF Generation | Puppeteer (headless Chrome) | Markdown → HTML → PDF |
| PPTX Generation | pptxgenjs | Executive summary slides |
| File Storage | Cloudflare R2 | S3-compatible. No egress fees. |
| Email | Resend | Transactional + sequences |
| Market Widget | TradingView Lightweight Charts | Embedded in dashboard. Free. |
| Monitoring | Sentry | Error tracking on frontend and API |
| Analytics | Posthog | Product analytics, funnel tracking |

### AI model routing by agent

All agents run on Anthropic Claude. DeepSeek was tested and discarded due to 14–30% hallucination rate on SEC filings (Block 11 financial extraction) and inability to handle 10-K filings reliably. Prompt caching is mandatory in production — system prompts and tier instructions are cached across all requests, reducing input cost to ~10% of base rate.

| Agent | Model | Rationale |
|---|---|---|
| Research Analyst | Sonnet 4.6 | Long-context filing ingestion, structured extraction |
| Journal Analyst | Sonnet 4.6 | Earnings call and document analysis |
| Quantitative Analyst | Sonnet 4.6 | Numerical reasoning, DCF and comparables |
| Technical Analyst | Sonnet 4.6 | OHLC pattern recognition, multi-timeframe analysis |
| Fundamental Analyst | Opus 4.7 | High-stakes buy-side reasoning, moat and quality |
| Risk Analyst | Opus 4.7 | High-stakes sell-side reasoning, downside scenarios |
| Sentiment & News | Haiku 4.5 | High-volume, low-complexity classification |
| Fact Checker | Opus 4.7 | Cross-reference verification against primary sources |
| Senior Analyst (synthesis) | Opus 4.7 | Final thesis integration across all agent outputs |

---

## 3. Repository Structure

```
synthesis/
├── app/                          # Next.js App Router
│   ├── (marketing)/              # Route group — public pages
│   │   ├── page.tsx              # Landing page
│   │   ├── pricing/page.tsx
│   │   └── layout.tsx
│   ├── (auth)/                   # Route group — auth pages
│   │   ├── login/page.tsx
│   │   ├── signup/page.tsx       # Not public — auto-created on purchase
│   │   └── layout.tsx
│   ├── (dashboard)/              # Route group — authenticated
│   │   ├── layout.tsx            # Auth guard + sidebar
│   │   ├── dashboard/page.tsx    # Home — redirect by account_type
│   │   ├── history/page.tsx      # Analysis history (all users)
│   │   ├── tickers/page.tsx      # Active tickers (subscribers only)
│   │   ├── watchlist/page.tsx    # Watchlist (subscribers only)
│   │   ├── earnings/page.tsx     # Earnings & updates (all users)
│   │   ├── market/page.tsx       # Market search (subscribers only)
│   │   └── settings/page.tsx
│   ├── (checkout)/               # Route group — purchase flow
│   │   ├── configure/page.tsx    # Step 1: ticker + tier selection
│   │   ├── addons/page.tsx       # Step 2: add-on selection
│   │   └── success/page.tsx      # Post-payment confirmation
│   ├── status/page.tsx           # Order status — email+code access
│   └── api/
│       ├── webhooks/
│       │   ├── stripe/route.ts   # Stripe webhook handler
│       │   └── n8n/route.ts      # n8n callback — report ready
│       ├── orders/route.ts
│       ├── tickers/
│       │   ├── route.ts          # List active tickers
│       │   └── swap/route.ts     # Ticker swap — charge + replace
│       ├── reports/route.ts
│       └── admin/
│           └── [...slug]/route.ts
├── components/
│   ├── ui/                       # Shadcn/ui primitives
│   ├── dashboard/
│   ├── checkout/
│   └── admin/
├── lib/
│   ├── supabase/
│   │   ├── client.ts             # Browser client
│   │   ├── server.ts             # Server client (RSC + API routes)
│   │   └── admin.ts              # Service role client (webhooks only)
│   ├── stripe.ts
│   ├── resend.ts
│   ├── r2.ts                     # Cloudflare R2 client
│   └── constants.ts              # Prices, plan limits, etc.
├── middleware.ts                 # Auth guard for /dashboard routes
├── supabase/
│   ├── migrations/               # SQL migration files
│   │   └── 001_initial_schema.sql
│   └── seed.sql                  # Dev seed data
└── n8n/
    └── workflows/                # Exported n8n workflow JSON files
        ├── one_shot_pipeline.json
        ├── subscription_pipeline.json
        ├── news_pulse.json
        └── earnings_detector.json
```

---

## 4. Environment Variables

Create `.env.local` at the project root. **Never commit this file.**

```bash
# Supabase
NEXT_PUBLIC_SUPABASE_URL=https://your-project.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=your-anon-key
SUPABASE_SERVICE_ROLE_KEY=your-service-role-key   # Server-only. Never expose to client.

# Stripe
NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY=pk_live_...
STRIPE_SECRET_KEY=sk_live_...
STRIPE_WEBHOOK_SECRET=whsec_...                    # From Stripe dashboard

# Stripe Price IDs (create these in Stripe dashboard first)
STRIPE_PRICE_CORE_ONSHOT=price_...
STRIPE_PRICE_PRIME_ONESHOT=price_...
STRIPE_PRICE_EDGE_ONESHOT=price_...
STRIPE_PRICE_ADDON_NEWS_PULSE=price_...
STRIPE_PRICE_ADDON_QUARTERLY=price_...
STRIPE_PRICE_ADDON_YEARLY=price_...
STRIPE_PRICE_SUB_CORE_MONTHLY=price_...
STRIPE_PRICE_SUB_CORE_ANNUAL=price_...
STRIPE_PRICE_SUB_PRIME_MONTHLY=price_...
STRIPE_PRICE_SUB_PRIME_ANNUAL=price_...
STRIPE_PRICE_SUB_EDGE_MONTHLY=price_...
STRIPE_PRICE_SUB_EDGE_ANNUAL=price_...
STRIPE_PRICE_TICKER_ACTIVATION=price_...          # $4.99 swap fee
STRIPE_PRICE_ONDEMAND_ANALYSIS=price_...          # $14.99 extra analysis

# n8n
N8N_WEBHOOK_SECRET=your-shared-secret             # Validates n8n → API calls
N8N_BASE_URL=https://your-instance.n8n.cloud

# Cloudflare R2
R2_ACCOUNT_ID=your-account-id
R2_ACCESS_KEY_ID=your-key-id
R2_SECRET_ACCESS_KEY=your-secret
R2_BUCKET_NAME=synthesis-reports
R2_PUBLIC_URL=https://reports.yourdomain.com      # Custom domain on R2 bucket

# Resend
RESEND_API_KEY=re_...
RESEND_FROM_EMAIL=reports@yourdomain.com
RESEND_FROM_NAME=Synthesis

# Financial Modeling Prep
FMP_API_KEY=your-key

# Tavily (web search for Research Agent)
TAVILY_API_KEY=tvly-...

# Anthropic (Claude — all agents: Opus 4.7, Sonnet 4.6, Haiku 4.5)
ANTHROPIC_API_KEY=sk-ant-...

# Internal
ADMIN_SECRET=long-random-string                   # Protects /api/admin routes
NEXT_PUBLIC_APP_URL=https://yourdomain.com
```

---

## 5. Database Schema

Run this migration first: `supabase/migrations/001_initial_schema.sql`

```sql
-- ============================================================
-- EXTENSIONS
-- ============================================================
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";
CREATE EXTENSION IF NOT EXISTS "pgcrypto";

-- ============================================================
-- USERS
-- Central user table. Linked to Supabase Auth via id.
-- account_type drives dashboard access control.
-- ============================================================
CREATE TABLE users (
  id                  UUID PRIMARY KEY REFERENCES auth.users(id) ON DELETE CASCADE,
  email               TEXT NOT NULL UNIQUE,
  full_name           TEXT,
  stripe_customer_id  TEXT UNIQUE,
  account_type        TEXT NOT NULL DEFAULT 'one_shot'
                        CHECK (account_type IN ('one_shot', 'subscriber', 'admin')),
  created_at          TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  last_login_at       TIMESTAMPTZ
);

-- ============================================================
-- ORDERS
-- Every one-shot purchase. One row per report purchased.
-- access_code_hash: bcrypt hash of the 8-char code sent by email.
-- ============================================================
CREATE TABLE orders (
  id                        UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id                   UUID NOT NULL REFERENCES users(id),
  ticker                    TEXT NOT NULL,
  product_tier              TEXT NOT NULL CHECK (product_tier IN ('core', 'prime', 'edge')),
  status                    TEXT NOT NULL DEFAULT 'pending'
                              CHECK (status IN ('pending','processing','delivered','failed','expired')),
  amount_cents              INTEGER NOT NULL,
  disclaimer_accepted       BOOLEAN NOT NULL DEFAULT FALSE,
  stripe_payment_intent_id  TEXT UNIQUE,
  access_code_hash          TEXT,
  access_code_expires_at    TIMESTAMPTZ,
  created_at                TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  delivered_at              TIMESTAMPTZ
);

-- ============================================================
-- ORDER ADD-ONS
-- One row per add-on purchased alongside a one-shot order.
-- updates_remaining: tracks how many report updates are left.
--   news_pulse = 0 (no report updates, only news)
--   quarterly_refresh = 1
--   yearly_sync = 4
-- ============================================================
CREATE TABLE order_addons (
  id                  UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  order_id            UUID NOT NULL REFERENCES orders(id) ON DELETE CASCADE,
  addon_type          TEXT NOT NULL CHECK (addon_type IN ('news_pulse','quarterly_refresh','yearly_sync')),
  amount_cents        INTEGER NOT NULL,
  status              TEXT NOT NULL DEFAULT 'active'
                        CHECK (status IN ('active','exhausted','expired','cancelled')),
  updates_remaining   INTEGER NOT NULL DEFAULT 0,
  starts_at           TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  expires_at          TIMESTAMPTZ NOT NULL
);

-- ============================================================
-- SUBSCRIPTIONS
-- One active row per subscriber. Linked to Stripe Billing.
-- on_demand_used resets to 0 on every billing period renewal.
-- ============================================================
CREATE TABLE subscriptions (
  id                      UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id                 UUID NOT NULL UNIQUE REFERENCES users(id),
  plan_type               TEXT NOT NULL CHECK (plan_type IN ('core','prime','edge')),
  billing_interval        TEXT NOT NULL CHECK (billing_interval IN ('monthly','annual')),
  stripe_subscription_id  TEXT NOT NULL UNIQUE,
  status                  TEXT NOT NULL DEFAULT 'active'
                            CHECK (status IN ('active','past_due','cancelled','paused')),
  tickers_limit           INTEGER NOT NULL,
  -- -1 = unlimited (Edge plan)
  on_demand_used          INTEGER NOT NULL DEFAULT 0,
  on_demand_limit         INTEGER NOT NULL,
  -- -1 = unlimited (Edge plan)
  current_period_start    TIMESTAMPTZ NOT NULL,
  current_period_end      TIMESTAMPTZ NOT NULL,
  cancelled_at            TIMESTAMPTZ,
  created_at              TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

-- ============================================================
-- ACTIVE TICKERS
-- One row per ticker slot in a subscription.
-- A ticker is "active" when status = 'active' and deactivated_at IS NULL.
-- activated_at is reset on every swap — used for the 90-day swap fee check.
-- swap_fee_paid: TRUE if this activation cost $4.99.
-- ============================================================
CREATE TABLE active_tickers (
  id                UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  subscription_id   UUID NOT NULL REFERENCES subscriptions(id) ON DELETE CASCADE,
  ticker            TEXT NOT NULL,
  status            TEXT NOT NULL DEFAULT 'active'
                      CHECK (status IN ('active','inactive')),
  activated_at      TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  deactivated_at    TIMESTAMPTZ,
  swap_fee_paid     BOOLEAN NOT NULL DEFAULT FALSE,
  swap_fee_cents    INTEGER NOT NULL DEFAULT 0,
  UNIQUE (subscription_id, ticker, status)
  -- Prevents the same ticker being active twice in the same subscription
);

-- ============================================================
-- WATCHLIST
-- Passive ticker tracking. Any user with an account can use this.
-- No limit, no cost, no analysis triggered.
-- ============================================================
CREATE TABLE watchlist (
  id          UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id     UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  ticker      TEXT NOT NULL,
  added_at    TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  UNIQUE (user_id, ticker)
);

-- ============================================================
-- REPORTS
-- The central asset. Every generated PDF/PPTX is one row.
-- Exactly one of order_id or subscription_id is NOT NULL.
-- storage_url_* are signed URLs or paths in Cloudflare R2.
-- download_expires_at: the 24h temporary download link window.
-- ============================================================
CREATE TABLE reports (
  id                    UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  order_id              UUID REFERENCES orders(id),
  subscription_id       UUID REFERENCES subscriptions(id),
  ticker                TEXT NOT NULL,
  report_type           TEXT NOT NULL CHECK (report_type IN ('core','prime','edge')),
  generation_status     TEXT NOT NULL DEFAULT 'queued'
                          CHECK (generation_status IN
                            ('queued','running','complete','failed')),
  storage_key_pdf       TEXT,
  storage_key_pptx      TEXT,
  generation_cost_cents INTEGER,
  generated_at          TIMESTAMPTZ,
  download_expires_at   TIMESTAMPTZ,
  created_at            TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  CONSTRAINT one_source_only CHECK (
    (order_id IS NOT NULL AND subscription_id IS NULL) OR
    (order_id IS NULL AND subscription_id IS NOT NULL)
  )
);

-- ============================================================
-- AGENT RUNS
-- One row per AI agent invocation per report.
-- Used for cost tracking and debugging.
-- ============================================================
CREATE TABLE agent_runs (
  id              UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  report_id       UUID NOT NULL REFERENCES reports(id) ON DELETE CASCADE,
  agent_name      TEXT NOT NULL,
  -- 'research', 'journal', 'quantitative', 'technical',
  -- 'fundamental', 'risk', 'sentiment', 'senior_analyst', 'fact_checker'
  model_used      TEXT NOT NULL,
  input_tokens    INTEGER,
  output_tokens   INTEGER,
  cost_cents      INTEGER,
  status          TEXT NOT NULL DEFAULT 'running'
                    CHECK (status IN ('running','complete','failed')),
  error_message   TEXT,
  started_at      TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  finished_at     TIMESTAMPTZ
);

-- ============================================================
-- EARNINGS CALENDAR
-- Populated and updated daily by an n8n cron job via FMP API.
-- status: 'upcoming' → 'confirmed' → 'reported'
-- ============================================================
CREATE TABLE earnings_calendar (
  id            UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  ticker        TEXT NOT NULL,
  report_date   DATE NOT NULL,
  fiscal_period TEXT,
  -- e.g. 'Q1 2026'
  status        TEXT NOT NULL DEFAULT 'upcoming'
                  CHECK (status IN ('upcoming','confirmed','reported')),
  fetched_at    TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  UNIQUE (ticker, report_date)
);

-- ============================================================
-- TICKER UPDATES
-- Tracks every automatic or on-demand update triggered.
-- trigger_type: what caused this update.
-- For one-shot: links to order_addon_id.
-- For subscriptions: links to active_ticker_id.
-- ============================================================
CREATE TABLE ticker_updates (
  id                  UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  active_ticker_id    UUID REFERENCES active_tickers(id),
  order_addon_id      UUID REFERENCES order_addons(id),
  earnings_id         UUID REFERENCES earnings_calendar(id),
  report_id           UUID REFERENCES reports(id),
  trigger_type        TEXT NOT NULL
                        CHECK (trigger_type IN
                          ('earnings_auto','on_demand','ticker_activation','swap')),
  status              TEXT NOT NULL DEFAULT 'pending'
                        CHECK (status IN ('pending','running','delivered','failed')),
  triggered_at        TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  delivered_at        TIMESTAMPTZ,
  CONSTRAINT one_source_only CHECK (
    (active_ticker_id IS NOT NULL AND order_addon_id IS NULL) OR
    (active_ticker_id IS NULL AND order_addon_id IS NOT NULL)
  )
);

-- ============================================================
-- NEWS ALERTS
-- One row per news item sent to a user.
-- Links to either order_addon (one-shot News Pulse) or
-- subscription (subscriber — all active tickers get news).
-- ============================================================
CREATE TABLE news_alerts (
  id                UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id           UUID NOT NULL REFERENCES users(id),
  order_addon_id    UUID REFERENCES order_addons(id),
  subscription_id   UUID REFERENCES subscriptions(id),
  ticker            TEXT NOT NULL,
  headline          TEXT NOT NULL,
  summary           TEXT,
  source_url        TEXT,
  category          TEXT CHECK (category IN
                      ('earnings','acquisition','rating_change',
                       'regulation','management','macro','other')),
  sent              BOOLEAN NOT NULL DEFAULT FALSE,
  published_at      TIMESTAMPTZ,
  sent_at           TIMESTAMPTZ,
  created_at        TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  CONSTRAINT one_source_only CHECK (
    (order_addon_id IS NOT NULL AND subscription_id IS NULL) OR
    (order_addon_id IS NULL AND subscription_id IS NOT NULL)
  )
);

-- ============================================================
-- INDEXES
-- Critical for query performance.
-- ============================================================
CREATE INDEX idx_orders_user_id ON orders(user_id);
CREATE INDEX idx_orders_status ON orders(status);
CREATE INDEX idx_active_tickers_subscription ON active_tickers(subscription_id, status);
CREATE INDEX idx_active_tickers_ticker ON active_tickers(ticker) WHERE status = 'active';
CREATE INDEX idx_earnings_calendar_ticker_date ON earnings_calendar(ticker, report_date);
CREATE INDEX idx_reports_order ON reports(order_id);
CREATE INDEX idx_reports_subscription ON reports(subscription_id);
CREATE INDEX idx_agent_runs_report ON agent_runs(report_id);
CREATE INDEX idx_news_alerts_user ON news_alerts(user_id, sent);
CREATE INDEX idx_news_alerts_addon ON news_alerts(order_addon_id) WHERE sent = FALSE;
```

---

## 6. Row-Level Security

Enable RLS on all tables. Users can only read their own data. The service role key (used in webhooks and n8n callbacks) bypasses RLS.

```sql
-- Enable RLS on all tables
ALTER TABLE users ENABLE ROW LEVEL SECURITY;
ALTER TABLE orders ENABLE ROW LEVEL SECURITY;
ALTER TABLE order_addons ENABLE ROW LEVEL SECURITY;
ALTER TABLE subscriptions ENABLE ROW LEVEL SECURITY;
ALTER TABLE active_tickers ENABLE ROW LEVEL SECURITY;
ALTER TABLE watchlist ENABLE ROW LEVEL SECURITY;
ALTER TABLE reports ENABLE ROW LEVEL SECURITY;
ALTER TABLE agent_runs ENABLE ROW LEVEL SECURITY;
ALTER TABLE earnings_calendar ENABLE ROW LEVEL SECURITY;
ALTER TABLE ticker_updates ENABLE ROW LEVEL SECURITY;
ALTER TABLE news_alerts ENABLE ROW LEVEL SECURITY;

-- Users: read and update own row only
CREATE POLICY "users_own" ON users
  FOR ALL USING (auth.uid() = id);

-- Orders: read own orders only
CREATE POLICY "orders_own" ON orders
  FOR SELECT USING (auth.uid() = user_id);

-- Order add-ons: read through order ownership
CREATE POLICY "order_addons_own" ON order_addons
  FOR SELECT USING (
    EXISTS (SELECT 1 FROM orders WHERE orders.id = order_addons.order_id
            AND orders.user_id = auth.uid())
  );

-- Subscriptions: read own subscription
CREATE POLICY "subscriptions_own" ON subscriptions
  FOR SELECT USING (auth.uid() = user_id);

-- Active tickers: read through subscription ownership
CREATE POLICY "active_tickers_own" ON active_tickers
  FOR SELECT USING (
    EXISTS (SELECT 1 FROM subscriptions WHERE subscriptions.id = active_tickers.subscription_id
            AND subscriptions.user_id = auth.uid())
  );

-- Watchlist: full CRUD on own rows
CREATE POLICY "watchlist_own" ON watchlist
  FOR ALL USING (auth.uid() = user_id);

-- Reports: read through order or subscription ownership
CREATE POLICY "reports_own" ON reports
  FOR SELECT USING (
    (order_id IS NOT NULL AND EXISTS (
      SELECT 1 FROM orders WHERE orders.id = reports.order_id
      AND orders.user_id = auth.uid()
    )) OR
    (subscription_id IS NOT NULL AND EXISTS (
      SELECT 1 FROM subscriptions WHERE subscriptions.id = reports.subscription_id
      AND subscriptions.user_id = auth.uid()
    ))
  );

-- Earnings calendar: public read (no PII)
CREATE POLICY "earnings_public_read" ON earnings_calendar
  FOR SELECT USING (TRUE);

-- News alerts: read own alerts
CREATE POLICY "news_alerts_own" ON news_alerts
  FOR SELECT USING (auth.uid() = user_id);

-- Ticker updates: read through active_ticker or order_addon ownership
CREATE POLICY "ticker_updates_own" ON ticker_updates
  FOR SELECT USING (
    (active_ticker_id IS NOT NULL AND EXISTS (
      SELECT 1 FROM active_tickers at
      JOIN subscriptions s ON s.id = at.subscription_id
      WHERE at.id = ticker_updates.active_ticker_id AND s.user_id = auth.uid()
    )) OR
    (order_addon_id IS NOT NULL AND EXISTS (
      SELECT 1 FROM order_addons oa
      JOIN orders o ON o.id = oa.order_id
      WHERE oa.id = ticker_updates.order_addon_id AND o.user_id = auth.uid()
    ))
  );

-- Agent runs: not exposed to users (admin only via service role)
-- No RLS policy needed — all queries via service role key
```

---

## 7. Authentication & Account Creation Flow

### New customer (one-shot purchase)

This is the most important flow. A customer pays before having an account.

```
1. Customer completes Stripe Checkout
2. Stripe fires webhook → POST /api/webhooks/stripe
3. Webhook handler (service role):
   a. Verify Stripe signature
   b. Check if user already exists (email lookup in users table)
   c. If NOT exists:
      - Create Supabase Auth user: supabase.auth.admin.createUser({ email, password: nanoid(16) })
      - Insert into users table (id = auth user id, account_type = 'one_shot')
      - Send welcome email via Resend with temporary password + login link
   d. If EXISTS: retrieve existing user_id
   e. Create order record
   f. Create order_addon records (if any add-ons purchased)
   g. Generate access_code (nanoid(8), uppercase)
   h. Store bcrypt(access_code) in orders.access_code_hash
   i. Set access_code_expires_at = NOW() + 30 days
   j. Create report record (generation_status = 'queued')
   k. Trigger n8n webhook → start AI pipeline
   l. Send order confirmation email with access_code and order status URL
```

### Returning customer login

Standard Supabase Auth email/password. No magic links in v1.

### Access code flow (unauthenticated order status)

The `/status` page allows unauthenticated access to order status for users who haven't yet logged in.

```
GET /status?email=x&code=y
1. Look up order by email (join users) where access_code_expires_at > NOW()
2. bcrypt.compare(code, access_code_hash)
3. If match: return order status + download links
4. If expired: show "Code expired" + login prompt
5. Never reveal whether the email exists (timing-safe comparison)
```

---

## 8. Stripe Integration

### Webhook events to handle

```typescript
// /api/webhooks/stripe/route.ts
switch (event.type) {
  // One-shot purchase completed
  case 'checkout.session.completed':
    await handleOneShot(event.data.object);
    break;

  // Subscription created (new subscriber)
  case 'customer.subscription.created':
    await handleSubscriptionCreated(event.data.object);
    break;

  // Subscription renewed (monthly/annual)
  case 'customer.subscription.updated':
    await handleSubscriptionUpdated(event.data.object);
    break;

  // Subscription cancelled
  case 'customer.subscription.deleted':
    await handleSubscriptionCancelled(event.data.object);
    break;

  // Payment failed (subscription)
  case 'invoice.payment_failed':
    await handlePaymentFailed(event.data.object);
    break;

  // Ticker swap fee or on-demand analysis charged
  case 'payment_intent.succeeded':
    await handleAdHocPayment(event.data.object);
    break;
}
```

### Stripe metadata conventions

Always pass metadata in Stripe sessions to identify context in webhooks:

```typescript
// One-shot checkout session
metadata: {
  type: 'oneshot',
  user_email: session.customer_email,
  ticker: 'AAPL',
  product_tier: 'edge',
  addons: 'news_pulse,quarterly_refresh',  // comma-separated
}

// Ticker swap payment intent
metadata: {
  type: 'ticker_swap',
  user_id: 'uuid',
  subscription_id: 'uuid',
  old_ticker: 'AAPL',
  new_ticker: 'MSFT',
  active_ticker_id: 'uuid',
}

// On-demand analysis payment intent
metadata: {
  type: 'ondemand_analysis',
  user_id: 'uuid',
  subscription_id: 'uuid',
  ticker: 'NVDA',
  report_type: 'edge',
}
```

### Subscription plan constants

```typescript
// lib/constants.ts
export const PLAN_LIMITS = {
  core:  { tickers: 5,  on_demand: 2  },
  prime: { tickers: 10, on_demand: 5  },
  edge:  { tickers: -1, on_demand: 20 }, // -1 = unlimited
} as const;

export const PRICES_CENTS = {
  oneshot: { core: 999, prime: 1499, edge: 2999 },
  addons:  { news_pulse: 499, quarterly_refresh: 1499, yearly_sync: 3499 },
  ticker_swap: 499,
  on_demand: 1499,
} as const;

export const ADDON_UPDATES = {
  news_pulse:          0,  // no report updates
  quarterly_refresh:   1,
  yearly_sync:         4,
} as const;

export const TICKER_SWAP_GRACE_DAYS = 90;
```

---

## 9. n8n Automation Workflows

n8n is the orchestration layer. All AI pipelines run inside n8n workflows. The Next.js API triggers them via webhook; n8n calls back when done.

### Workflow: One-Shot Report Pipeline

**Trigger:** Webhook from `/api/webhooks/stripe` after payment confirmation.

**Payload:**
```json
{
  "report_id": "uuid",
  "order_id": "uuid",
  "ticker": "AAPL",
  "product_tier": "edge",
  "secret": "N8N_WEBHOOK_SECRET"
}
```

**Steps:**
```
1. Validate secret header
2. Update reports.generation_status = 'running'
3. Fetch ticker data from FMP API (financials, ratios, price history)
4. PARALLEL LAYER 1:
   ├── Research Analyst (Sonnet 4.6) — SEC filings ingestion, web search via Tavily
   ├── Journal Analyst (Sonnet 4.6) — earnings call transcripts, document analysis
   ├── Quantitative Analyst (Sonnet 4.6) — DCF, ratios, comparables from FMP data
   └── Technical Analyst (Sonnet 4.6) — OHLC patterns, multi-timeframe indicators [skip if tier = core]
5. PARALLEL LAYER 2 (receives Layer 1 outputs):
   ├── Fundamental Analyst (Opus 4.7) — buy-side thesis: moat, management, quality
   ├── Risk Analyst (Opus 4.7) — sell-side thesis: downside scenarios, regulatory, macro
   └── Sentiment & News (Haiku 4.5) — news sentiment, analyst ratings
6. Senior Analyst (Opus 4.7) — synthesizes all outputs into full thesis markdown
7. Fact Checker (Opus 4.7) — cross-checks key claims against primary source data
8. Generate PDF via Puppeteer (markdown → HTML template → PDF)
9. If tier = edge: Generate PPTX via pptxgenjs
10. Upload PDF + PPTX to Cloudflare R2
11. Update reports: storage_key_pdf, storage_key_pptx, generation_status = 'complete'
12. Log all agent_runs (tokens, cost, model)
13. POST /api/webhooks/n8n { report_id, status: 'complete' }
14. API sends delivery email with download link (24h signed URL from R2)
```

### Workflow: Subscription Report Pipeline

Same as one-shot pipeline but triggered by:
- `ticker_activation` (user adds a new ticker)
- `earnings_auto` (cron detects earnings date reached)
- `on_demand` (user requests additional analysis)

**Payload additions:**
```json
{
  "subscription_id": "uuid",
  "active_ticker_id": "uuid",
  "trigger_type": "ticker_activation"
}
```

### Workflow: Earnings Detector (Cron)

**Schedule:** Daily at 06:00 UTC.

**Steps:**
```
1. Fetch all distinct tickers from active_tickers WHERE status = 'active'
2. For each ticker: check earnings_calendar WHERE report_date = TODAY
3. Also: update earnings_calendar with fresh FMP data for next 90 days
4. For each ticker with earnings TODAY:
   a. Get all active_tickers rows for this ticker
   b. For each: trigger Subscription Report Pipeline (trigger_type = 'earnings_auto')
5. For one-shot users with quarterly_refresh or yearly_sync:
   a. Check order_addons WHERE addon_type IN ('quarterly_refresh','yearly_sync')
     AND status = 'active' AND updates_remaining > 0
   b. Match ticker to earnings_calendar.report_date = TODAY
   c. Trigger One-Shot Report Pipeline for matching orders
   d. Decrement order_addons.updates_remaining
   e. If updates_remaining = 0: set status = 'exhausted'
```

### Workflow: News Pulse (Cron)

**Schedule:** Every weekday at 08:00 UTC.

**Steps:**
```
1. Fetch all distinct tickers that have active news coverage:
   - active_tickers WHERE status = 'active' (subscribers)
   - order_addons WHERE addon_type IN ('news_pulse','quarterly_refresh','yearly_sync')
     AND status = 'active' AND expires_at > NOW() (one-shot)
2. For each ticker: search Tavily for news in last 24h
3. Filter by relevance categories (earnings, M&A, ratings, regulation, management)
4. For each relevant article:
   a. Insert into news_alerts (sent = FALSE)
   b. Trigger email send via Resend
   c. Update news_alerts.sent = TRUE
```

### n8n → API Callback

When a report is ready, n8n calls back:

```
POST /api/webhooks/n8n
Headers: { x-n8n-secret: N8N_WEBHOOK_SECRET }
Body: { report_id: "uuid", status: "complete" | "failed", error?: "..." }
```

The API handler:
1. Validates the secret header
2. Fetches the report and associated order/subscription
3. Generates a 24h signed URL from R2 for each file
4. Updates `download_expires_at`
5. Sends the delivery email via Resend

---

## 10. AI Agent Architecture

### Agent definitions

Each agent is a prompt file in `n8n/agents/`. All agents run on Anthropic Claude. Prompt caching is mandatory — the system prompt and tier instructions for each agent are cached across all requests, reducing input cost to ~10% of base rate.

| Agent | Model | Input | Output |
|---|---|---|---|
| Research Analyst | Sonnet 4.6 | SEC filings, ticker, sector, recent news (Tavily) | Structured research dossier markdown |
| Journal Analyst | Sonnet 4.6 | Earnings call transcripts, investor day materials | Management narrative summary markdown |
| Quantitative Analyst | Sonnet 4.6 | FMP financial data (5–10yr) | Ratios, DCF, comparables markdown |
| Technical Analyst | Sonnet 4.6 | OHLC data (1d, 1w, 1m) | Multi-timeframe technical setup markdown |
| Fundamental Analyst | Opus 4.7 | Research + Quant + Journal outputs | Buy-side thesis: moat, management, quality |
| Risk Analyst | Opus 4.7 | All Layer 1 outputs | Sell-side thesis: downside scenarios, regulatory, macro |
| Sentiment & News | Haiku 4.5 | News headlines, analyst ratings | Sentiment classification summary |
| Senior Analyst | Opus 4.7 | All agent outputs | Full investment thesis markdown |
| Fact Checker | Opus 4.7 | Senior Analyst output + primary source data | Verified/flagged final thesis |

### Tier routing

```typescript
// In n8n workflow logic
const agentsForTier = {
  core: [
    'research', 'journal', 'quantitative',
    'fundamental', 'risk', 'sentiment',
    'senior_analyst', 'fact_checker'
  ],
  prime: [
    'research', 'journal', 'quantitative', 'technical',
    'fundamental', 'risk', 'sentiment',
    'senior_analyst', 'fact_checker'
  ],
  edge: [
    'research', 'journal', 'quantitative', 'technical',
    'fundamental', 'risk', 'sentiment',
    'senior_analyst', 'fact_checker'
  ],
  // Technical Analyst runs only on Prime and Edge.
  // Edge = Prime + DCF depth + comparables expansion + PPTX generation.
}
```

### Cost estimates per report (with prompt caching)

DeepSeek was discarded in May 2026 after testing revealed 14–30% hallucination rates on SEC filing extraction. The unit economics below assume mandatory prompt caching across all agents.

| Tier | Estimated cost | Notes |
|---|---|---|
| Core | ~$2.80 | 8 agents, no Technical |
| Prime | ~$4.50 | 9 agents, Technical included |
| Edge | ~$7.50 | 9 agents, expanded depth, PPTX generation |

**Gross margin per product (with caching):**

| Product | Price | Cost | Gross margin |
|---|---|---|---|
| Core one-shot | $9.99 | ~$2.80 | ~72% |
| Prime one-shot | $14.99 | ~$4.50 | ~70% |
| Edge one-shot | $29.99 | ~$7.50 | ~75% |

*Subscription unit economics scale favorably as the same ticker generates multiple reports per year (initial activation + 4 earnings updates), spreading prompt caching benefits further.*

---

## 11. File Generation & Storage

### PDF generation (Puppeteer)

The Senior Analyst outputs Markdown. The pipeline converts it:

```
Markdown → HTML (custom template) → PDF (Puppeteer headless Chrome)
```

The HTML template (`n8n/templates/report.html`) includes:
- Synthesis header with logo
- Ticker + date + tier badge
- Disclaimer block at top and bottom
- Professional typography (Inter font, financial table styles)
- Page numbers in footer

### PPTX generation (pptxgenjs) — Edge tier only

The Senior Analyst also outputs a structured JSON summary (separate prompt) that pptxgenjs uses to build slides:
- Slide 1: Cover (ticker, date, Synthesis branding)
- Slide 2: Investment thesis summary (3 bullets)
- Slide 3: Financial highlights (key ratios table)
- Slide 4: DCF valuation (base / bull / bear)
- Slide 5: Risk factors
- Slide 6: Disclaimer

### Cloudflare R2 storage

File key convention:
```
reports/{user_id}/{order_id_or_subscription_id}/{ticker}_{date}_{tier}.pdf
reports/{user_id}/{order_id_or_subscription_id}/{ticker}_{date}_{tier}.pptx
```

Download links: generate signed URLs with 24h expiry using the R2 S3-compatible API.

```typescript
// lib/r2.ts
import { S3Client, GetObjectCommand } from '@aws-sdk/client-s3';
import { getSignedUrl } from '@aws-sdk/s3-request-presigner';

export async function getDownloadUrl(key: string): Promise<string> {
  const command = new GetObjectCommand({ Bucket: process.env.R2_BUCKET_NAME, Key: key });
  return getSignedUrl(r2Client, command, { expiresIn: 86400 }); // 24h
}
```

---

## 12. Email System

All emails sent via Resend. Templates built with React Email.

### Transactional emails

| Trigger | Template | Key content |
|---|---|---|
| Account created | `welcome` | Temporary password + login URL |
| Order confirmed | `order_confirmed` | Ticker, tier, access code, status URL |
| Report delivered | `report_delivered` | Download links (24h), login CTA |
| Subscription started | `subscription_welcome` | Dashboard URL, how to add tickers |
| Ticker activated | `ticker_activated` | Ticker name, generation started |
| Report updated | `report_updated` | Earnings update delivered, download links |
| Subscription renewing | `renewal_reminder` | Sent 5 days before period end |
| Payment failed | `payment_failed` | Stripe hosted invoice link |
| News Pulse digest | `news_digest` | Up to 5 articles, ticker, category tags |

### Post-purchase email sequences

Sequences are n8n workflows triggered on customer events. They use time delays between steps.

**Sequence A — One-shot, no add-ons:**
- Day 0: Report delivered email
- Day 1: "How to read your Synthesis report" (educational)
- Day 3: "Imagine having this for 5 tickers" (subscription intro)
- Day 5: Discount offer — 20% off Core subscription (48h)
- Day 7: Discount reminder (last chance)
- Day 14: Educational content
- Day 21: Social proof + CTA
- Day 30: Code expiry reminder + new purchase offer

**Sequence B — One-shot with News Pulse:**
- Sequence A + daily news digests (weekdays)
- Day 45: Add-on expiry warning + subscription upgrade offer

**Sequence C — One-shot with Quarterly/Yearly Sync:**
- Sequence A (condensed)
- On each update delivery: "Subscribers get this automatically for all tickers" + upgrade CTA

**Sequence D — New subscriber:**
- Day 0: Welcome + dashboard link
- Day 1: Onboarding — how to add your first ticker
- Day 7: Follow-up — "Haven't added a ticker yet?" (if 0 tickers)
- Day 25: Renewal reminder with value summary (reports generated, updates received)

---

## 13. Dashboard — Frontend Routes & Access Control

### Middleware

```typescript
// middleware.ts
import { createMiddlewareClient } from '@supabase/auth-helpers-nextjs';
import { NextResponse } from 'next/server';

export async function middleware(req) {
  const res = NextResponse.next();
  const supabase = createMiddlewareClient({ req, res });
  const { data: { session } } = await supabase.auth.getSession();

  const isDashboard = req.nextUrl.pathname.startsWith('/dashboard') ||
                      req.nextUrl.pathname.startsWith('/history') ||
                      req.nextUrl.pathname.startsWith('/tickers') ||
                      req.nextUrl.pathname.startsWith('/watchlist') ||
                      req.nextUrl.pathname.startsWith('/earnings') ||
                      req.nextUrl.pathname.startsWith('/market');

  if (isDashboard && !session) {
    return NextResponse.redirect(new URL('/login', req.url));
  }
  return res;
}
```

### Route access by account type

| Route | one_shot | subscriber | admin |
|---|---|---|---|
| `/dashboard` | ✓ | ✓ | ✓ |
| `/history` | ✓ (own reports) | ✓ (all reports) | ✓ |
| `/earnings` | ✓ (add-ons only) | ✓ (all tickers) | ✓ |
| `/tickers` | ✗ → upgrade CTA | ✓ | ✓ |
| `/watchlist` | ✗ → upgrade CTA | ✓ | ✓ |
| `/market` | ✗ → upgrade CTA | ✓ | ✓ |
| `/settings` | ✓ | ✓ | ✓ |
| `/admin` | ✗ | ✗ | ✓ |

For `one_shot` users visiting `/tickers`, `/watchlist`, or `/market`: show the page with a full-screen upgrade prompt overlay rather than a 404. This keeps the upgrade funnel visible.

### Dashboard home — content by account type

```typescript
// What /dashboard renders based on account_type:

// one_shot:
// - Summary card: reports purchased, add-ons active
// - Recent reports (last 3)
// - Active add-ons with days remaining
// - Upcoming earnings for contracted tickers
// - 3x upgrade CTAs (persistent banner, sidebar, inline cards)

// subscriber:
// - Summary card: active tickers / plan limit, on-demand analyses remaining
// - Active tickers with next earnings date
// - Recent reports
// - Upcoming earnings calendar (all tickers)
// - Watchlist preview
```

---

## 14. Ticker Swap Logic

### API endpoint

```typescript
// POST /api/tickers/swap
// Body: { active_ticker_id: string, new_ticker: string }
// Auth: required (subscriber only)

export async function POST(req: Request) {
  const { active_ticker_id, new_ticker } = await req.json();
  const supabase = createServerClient();
  const { data: { user } } = await supabase.auth.getUser();

  // 1. Fetch current active_ticker — verify ownership via subscription → user
  const { data: activeTicker } = await supabase
    .from('active_tickers')
    .select('*, subscriptions(user_id)')
    .eq('id', active_ticker_id)
    .eq('status', 'active')
    .single();

  if (activeTicker.subscriptions.user_id !== user.id) {
    return Response.json({ error: 'Unauthorized' }, { status: 403 });
  }

  // 2. Calculate days since activation
  const daysSinceActivation = differenceInDays(new Date(), new Date(activeTicker.activated_at));
  const requiresFee = daysSinceActivation < TICKER_SWAP_GRACE_DAYS;

  if (requiresFee) {
    // Return flag — frontend shows warning modal with confirmation
    return Response.json({
      requires_fee: true,
      fee_cents: PRICES_CENTS.ticker_swap,
      days_active: daysSinceActivation,
    });
  }

  // 3. Free swap — execute immediately
  await executeSwap(supabase, activeTicker, new_ticker, false);
  return Response.json({ success: true });
}

// Confirm paid swap (called after Stripe payment succeeds)
// POST /api/tickers/swap/confirm
// Body: { payment_intent_id: string, active_ticker_id: string, new_ticker: string }
```

### executeSwap function

```typescript
async function executeSwap(supabase, activeTicker, newTicker, feePaid: boolean) {
  // 1. Deactivate old ticker
  await supabase.from('active_tickers').update({
    status: 'inactive',
    deactivated_at: new Date().toISOString(),
  }).eq('id', activeTicker.id);

  // 2. Insert new active ticker
  const { data: newActiveTicker } = await supabase.from('active_tickers').insert({
    subscription_id: activeTicker.subscription_id,
    ticker: newTicker,
    status: 'active',
    activated_at: new Date().toISOString(),
    swap_fee_paid: feePaid,
    swap_fee_cents: feePaid ? PRICES_CENTS.ticker_swap : 0,
  }).select().single();

  // 3. Create ticker_update record
  await supabase.from('ticker_updates').insert({
    active_ticker_id: newActiveTicker.id,
    trigger_type: 'swap',
    status: 'pending',
  });

  // 4. Trigger n8n report generation pipeline
  await triggerN8nPipeline('subscription_pipeline', {
    active_ticker_id: newActiveTicker.id,
    subscription_id: activeTicker.subscription_id,
    ticker: newTicker,
    trigger_type: 'swap',
  });
}
```

---

## 15. Earnings Detection & Auto-Update Flow

The n8n Earnings Detector cron runs daily at 06:00 UTC and:

1. Queries `active_tickers` for all active tickers
2. Queries `earnings_calendar` for tickers with `report_date = CURRENT_DATE`
3. For each match: fires the subscription report pipeline
4. Also checks `order_addons` for one-shot users with active Quarterly/Yearly Sync

### Keeping earnings_calendar fresh

The same cron also refreshes the calendar:

```
For each unique ticker in active_tickers:
  → FMP endpoint: /v3/earning_calendar?symbol={ticker}&from={today}&to={today+90days}
  → Upsert into earnings_calendar (ticker, report_date) ON CONFLICT DO UPDATE
```

---

## 16. Admin Panel

The admin panel lives at `/admin` and is protected by both authentication (`account_type = 'admin'`) and a secondary check against `ADMIN_SECRET` in the API routes.

### Admin views

**Dashboard overview:**
- MRR / ARR (from Stripe)
- Active subscribers by plan
- One-shot orders this month
- Report generation queue status (from `reports` table)
- Total API cost this month (sum of `agent_runs.cost_cents`)

**Order queue:**
- Table: all orders with `generation_status IN ('queued','running','failed')`
- Manual retry button → calls n8n webhook
- View agent run logs per report

**User management:**
- Search by email
- View account type, subscription, order history
- Manual account_type override (e.g. upgrade/downgrade)

**Cost monitor:**
- Per-report cost (sum of agent_runs by report_id)
- Per-agent breakdown (avg cost per agent_name)
- Daily/weekly/monthly totals

**Email funnel status:**
- Active sequences per user segment
- Open rates (via Resend webhooks)

### Admin API protection

```typescript
// All /api/admin/* routes check:
const secret = req.headers.get('x-admin-secret');
if (secret !== process.env.ADMIN_SECRET) {
  return Response.json({ error: 'Forbidden' }, { status: 403 });
}

// Additionally verify the authenticated user has account_type = 'admin'
const { data: userRecord } = await supabaseAdmin
  .from('users').select('account_type').eq('id', user.id).single();
if (userRecord.account_type !== 'admin') {
  return Response.json({ error: 'Forbidden' }, { status: 403 });
}
```

---

## 17. Deployment

### Services and where they run

| Service | Platform | URL |
|---|---|---|
| Frontend + API routes | Vercel | yourdomain.com |
| Database + Auth | Supabase | auto-managed |
| n8n automation | n8n Cloud | n8n.io/cloud |
| File storage | Cloudflare R2 | reports.yourdomain.com |
| Email | Resend | — |

### Vercel configuration

```json
// vercel.json
{
  "functions": {
    "app/api/webhooks/stripe/route.ts": {
      "maxDuration": 30
    },
    "app/api/webhooks/n8n/route.ts": {
      "maxDuration": 30
    }
  }
}
```

> **Important:** Stripe webhooks require the raw request body for signature verification. In Next.js App Router, use `req.text()` before parsing, not `req.json()`.

### Stripe webhook configuration

In the Stripe dashboard, set the webhook endpoint to:
```
https://yourdomain.com/api/webhooks/stripe
```

Events to subscribe:
- `checkout.session.completed`
- `customer.subscription.created`
- `customer.subscription.updated`
- `customer.subscription.deleted`
- `invoice.payment_failed`
- `payment_intent.succeeded`

### Cloudflare R2 setup

1. Create bucket named `synthesis-reports`
2. Enable public access or set a custom domain (`reports.yourdomain.com`)
3. Create an API token with `Object Read & Write` on the bucket
4. Set CORS policy to allow only `yourdomain.com`

### Supabase setup

1. Create project
2. Run `supabase/migrations/001_initial_schema.sql` via the SQL editor
3. Enable Email Auth provider
4. Disable email confirmation (we control account creation via webhook)
5. Set `SUPABASE_SERVICE_ROLE_KEY` — never expose this on the client

---

## 18. Development Phases

### Phase 0 — Environment setup (Week 1)
- [ ] Register domain
- [ ] Create accounts: Stripe, Supabase, Vercel, n8n Cloud, Cloudflare, Resend, Anthropic, FMP, Tavily
- [ ] Create Stripe products and price IDs for all SKUs
- [ ] Set up Supabase project, run migration
- [ ] Deploy empty Next.js app to Vercel
- [ ] Configure all environment variables

### Phase 1 — Purchase flow + report delivery (Weeks 2–5)
- [ ] Landing page (marketing copy, pricing blocks, CTAs)
- [ ] Configure page (ticker input, tier selection)
- [ ] Add-ons page (News Pulse, Quarterly, Yearly — pre-Stripe)
- [ ] Stripe Checkout integration (one-shot)
- [ ] Stripe webhook handler → account creation → order record
- [ ] Order status page (`/status` — email + code)
- [ ] n8n: minimal pipeline (1 agent → PDF → email)
- [ ] Resend: transactional emails (confirmation, delivery)
- [ ] Cloudflare R2: upload and signed URL generation

### Phase 2 — AI agent pipeline (Weeks 6–8)
- [ ] All 9 agent prompts written and tested (Research, Journal, Quantitative, Technical, Fundamental, Risk, Sentiment & News, Senior Analyst, Fact Checker)
- [ ] n8n: full parallel pipeline (3-layer flow)
- [ ] FMP API integration (financial data)
- [ ] Tavily integration (web research)
- [ ] Claude integration in n8n (Opus 4.7, Sonnet 4.6, Haiku 4.5 with prompt caching)
- [ ] Agent cost logging (agent_runs table)
- [ ] PDF template (Puppeteer — professional layout, disclaimer)
- [ ] PPTX generation (pptxgenjs — Edge tier)

### Phase 3 — Dashboard (Weeks 9–11)
- [ ] Supabase Auth — login/logout
- [ ] Middleware — route protection
- [ ] Dashboard home (one-shot view)
- [ ] Analysis history (`/history`)
- [ ] Earnings & updates (`/earnings` — one-shot view)
- [ ] Settings page

### Phase 4 — Subscriptions (Weeks 12–14)
- [ ] Stripe Billing integration
- [ ] Subscription webhook handlers (created, updated, cancelled, failed)
- [ ] Active tickers management (`/tickers`)
- [ ] Ticker swap flow (90-day check, modal, Stripe payment, executeSwap)
- [ ] Watchlist (`/watchlist`)
- [ ] Market search with TradingView widget (`/market`)
- [ ] Dashboard home (subscriber view)
- [ ] n8n: subscription pipeline (ticker_activation, earnings_auto, on_demand)

### Phase 5 — Automation & emails (Weeks 15–16)
- [ ] n8n: Earnings Detector cron
- [ ] n8n: News Pulse cron
- [ ] n8n: post-purchase email sequences (A, B, C, D)
- [ ] Resend: all email templates (React Email)

### Phase 6 — Admin panel (Week 17)
- [ ] `/admin` route with account_type guard
- [ ] Overview metrics (MRR, orders, queue)
- [ ] Order queue with manual retry
- [ ] User management
- [ ] Cost monitor (agent_runs aggregates)

### Phase 7 — Production hardening (Week 18+)
- [ ] Sentry error tracking (frontend + API)
- [ ] Posthog analytics (funnel events)
- [ ] n8n error handling and retry logic
- [ ] Rate limiting on API routes
- [ ] Load testing
- [ ] Security audit (RLS policies, env var exposure, webhook signature verification)

---

*This document is the single source of truth for the Synthesis technical implementation.*
*Update it when architectural decisions change. Do not let it drift from the actual codebase.*
