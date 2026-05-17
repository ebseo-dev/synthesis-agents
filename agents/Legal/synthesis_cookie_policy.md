# Cookie Policy

**Synthesis — Automated Investment Analysis Platform**
**Effective date:** [DATE TO BE INSERTED UPON PUBLICATION]
**Last updated:** [DATE TO BE INSERTED UPON PUBLICATION]
**Version:** 1.0

---

## 1. Who We Are and How to Contact Us

This Cookie Policy is issued by:

**[FULL LEGAL NAME OF OPERATOR]**
Self-employed individual (autónomo)
Tax identification number: [NIF/NIE TO BE INSERTED]
Address: [ADDRESS TO BE INSERTED], Spain

Email (data protection enquiries): [privacy@synthesis.ai]

Synthesis is operated from Spain and is subject to the **ePrivacy Directive (2002/58/EC)** as transposed into Spanish law by the **Ley 34/2002, de 11 de julio, de Servicios de la Sociedad de la Información y de Comercio Electrónico (LSSI)** and its amendments, as well as the **General Data Protection Regulation (GDPR)** for all personal data processed through cookies.

This Cookie Policy forms part of, and must be read in conjunction with, the Synthesis **Privacy Policy** (available at [privacy policy URL]) and the **Terms and Conditions** (available at [T&C URL]).

---

## 2. What Are Cookies?

Cookies are small text files that are placed on your device (computer, smartphone, tablet) by a website when you visit it. They are widely used to make websites work efficiently, to remember your preferences, and to provide information to the website operator.

Cookies are not the only tracking technology that can be used on websites. The term "cookies" in this Policy is used as a general term to cover cookies and other similar technologies that read or write information to your device, including:

- **Session storage** — temporary data storage within your browser tab that is cleared when the tab is closed.
- **Local storage** — longer-term data storage within your browser that persists across sessions.
- **Pixel tags / web beacons** — small transparent images embedded in web pages or emails that register when they are loaded.
- **Fingerprinting** — the use of device or browser characteristics to identify a user.

**Synthesis does not use pixel tags, web beacons, or fingerprinting technologies.** The tracking technologies used by Synthesis are limited to cookies and browser session/local storage as described in this Policy.

---

## 3. Why Synthesis Uses Cookies

Synthesis uses cookies and similar technologies for a small and clearly defined set of purposes:

1. **To keep you logged in** — So that you do not have to re-authenticate every time you navigate between pages of the dashboard.
2. **To protect your account and our platform** — To prevent cross-site request forgery (CSRF) attacks and other security threats.
3. **To understand how users interact with the platform** — Anonymous, aggregated analytics that help us improve the Service (with your consent).
4. **To detect and diagnose technical errors** — So that we can identify and fix bugs quickly (with your consent).

Synthesis does **not** use cookies for:
- Advertising or retargeting
- Tracking your browsing activity on other websites
- Building advertising profiles
- Selling or sharing your data with third-party advertisers
- Any purpose unrelated to the operation and improvement of the Synthesis platform

---

## 4. The Cookies Synthesis Uses

The following tables describe every cookie and storage item that Synthesis may set on your device, organised by category.

### 4.1 Strictly Necessary Cookies

Strictly necessary cookies are essential for the website and the authenticated dashboard to function. They cannot be disabled without breaking core functionality. **These cookies do not require your consent** under the ePrivacy Directive and its national transpositions.

| Cookie name | Provider | Purpose | Type | Duration |
|---|---|---|---|---|
| `sb-[project-ref]-auth-token` | Supabase | Stores your authentication session token. Required to maintain your logged-in state in the dashboard. Without this cookie, you would be logged out on every page navigation. | HTTP cookie (HttpOnly, Secure) | 7 days (if "remember me" is selected) / Session (if not selected) |
| `sb-[project-ref]-auth-token-code-verifier` | Supabase | Used as part of the PKCE (Proof Key for Code Exchange) flow during authentication. Prevents authorization code interception attacks. | HTTP cookie (HttpOnly, Secure) | Session |
| `__Host-next-auth.csrf-token` | Next.js | Cross-Site Request Forgery (CSRF) protection token. Ensures that form submissions and API requests originate from the Synthesis platform and not from a third-party site. | HTTP cookie (HttpOnly, Secure, SameSite=Strict) | Session |
| `__Secure-next-auth.callback-url` | Next.js | Stores the URL to redirect to after successful authentication. Used to return the user to the page they were trying to access before being asked to log in. | HTTP cookie (Secure) | Session |

**Technical note for the development team:** All strictly necessary cookies must be set with the `Secure` flag (HTTPS only), the `HttpOnly` flag where applicable (prevents JavaScript access), and a `SameSite` attribute (`Strict` or `Lax` depending on the use case) to provide maximum protection against common web attacks. These attributes are mandatory, not optional.

---

### 4.2 Analytics Cookies

Analytics cookies help Synthesis understand how users interact with the platform — which pages are visited most, where users drop off in the checkout funnel, and which features are used. All data collected through these cookies is **anonymous and aggregated**; it is not linked to any individual user's email address, name, or account identifier.

**These cookies require your consent.** They are only set after you have affirmatively accepted analytics cookies via the cookie consent banner.

| Cookie name | Provider | Purpose | Type | Duration |
|---|---|---|---|---|
| `ph_[api_key]_posthog` | PostHog (EU Cloud) | Stores an anonymous session identifier used to track events within a single browsing session (e.g., page views, button clicks, funnel step completions). No personal data is stored in this cookie. | HTTP cookie | 1 year |
| `ph_[api_key]_posthog_ses` | PostHog (EU Cloud) | Session-specific analytics cookie. Distinguishes between separate browsing sessions by the same anonymous user. | HTTP cookie | 30 minutes (session) |
| `__ph_opt_in_out_[api_key]` | PostHog (EU Cloud) | Stores your consent preference for PostHog analytics. If you opt out, this cookie ensures that PostHog does not collect further analytics data from your device. | HTTP cookie | 1 year |

**What PostHog collects:** Page URLs visited, button and link clicks, time spent on each page, referral source (how you arrived at the Synthesis website), device type and browser type (aggregated — not used for fingerprinting), checkout funnel steps completed or abandoned. PostHog does **not** collect: email addresses, names, financial data, ticker symbols requested, or report content.

**PostHog configuration:** Synthesis uses the PostHog EU Cloud instance (hosted in Frankfurt, Germany). IP address anonymization is enabled. No identify calls that link analytics events to a personal email address are made on unauthenticated pages.

---

### 4.3 Error Monitoring Cookies

Error monitoring cookies allow Synthesis to detect, log, and diagnose technical errors in real time, enabling faster bug resolution and a more stable platform.

**These cookies require your consent.** They are only set after you have affirmatively accepted functional/monitoring cookies via the cookie consent banner.

| Cookie name | Provider | Purpose | Type | Duration |
|---|---|---|---|---|
| `sentry-sc` | Sentry | Stores an anonymous session identifier used to group error reports from the same browsing session. Helps Sentry's engineers understand the sequence of events leading to an error. No personal data (name, email) is stored in this cookie. | HTTP cookie | Session |

**What Sentry collects via this cookie:** Anonymous session ID, error stack traces, page URL at the time of the error, browser and device type. Sentry is configured with `beforeSend` hooks that strip any personal data (including email addresses, access codes, and user names) from error payloads before they are transmitted. The user ID (a UUID, not an email address) may be included in error reports for authenticated sessions to assist debugging, but it is never combined with the user's email in Sentry's interface.

**Sentry configuration:** Sentry error reports are scrubbed of PII before transmission. Session replay (if available on the applicable Sentry plan) is **not** enabled. Performance tracing data collected by Sentry does not include personal data.

---

### 4.4 Cookies Synthesis Does NOT Use

For complete transparency, Synthesis explicitly confirms that it does **not** use the following categories of cookies or tracking technologies:

| Category | Examples | Used by Synthesis? |
|---|---|---|
| Advertising / targeting cookies | Google Ads, Meta Pixel, LinkedIn Insight Tag | ❌ No |
| Social media tracking pixels | Facebook Pixel, Twitter/X Pixel, TikTok Pixel | ❌ No |
| Cross-site tracking | Third-party cookies that follow you across websites | ❌ No |
| Affiliate tracking cookies | Impact, ShareASale, CJ Affiliate | ❌ No |
| A/B testing cookies (third-party) | Optimizely, VWO | ❌ No |
| Live chat cookies | Intercom, Drift, Zendesk | ❌ No |
| Browser fingerprinting | Canvas fingerprinting, font detection | ❌ No |
| Video/media tracking | YouTube embedded player cookies | ❌ No |

---

## 5. Cookie Consent — How It Works

### 5.1 The consent banner

When you first visit the Synthesis website, a cookie consent banner is displayed at the bottom of the screen. The banner presents the following options:

- **"Accept all cookies"** — Sets all cookies described in Section 4, including analytics (PostHog) and error monitoring (Sentry) cookies.
- **"Reject non-essential cookies"** — Sets only strictly necessary cookies (Section 4.1). Analytics and error monitoring cookies are not set.
- **"Manage preferences"** — Opens the cookie preference centre, where you can enable or disable each cookie category individually.

Strictly necessary cookies (Section 4.1) are set regardless of your choice, as they are essential for the website to function.

### 5.2 What constitutes valid consent

Your consent to analytics and error monitoring cookies is valid only if it is:

- **Freely given** — Refusing non-essential cookies must not prevent you from using the Service. The "Reject non-essential cookies" option must be as prominent as the "Accept all" option.
- **Specific** — You consent to analytics cookies and error monitoring cookies separately (via the preference centre), not as a single bundled consent.
- **Informed** — You have access to this Cookie Policy before making your choice.
- **Unambiguous** — Consent requires an affirmative action (clicking a button). Pre-ticked boxes or inferred consent from continued browsing are not used.

### 5.3 Withdrawing or changing your consent

You may change your cookie preferences at any time by:

- Clicking the **"Cookie Settings"** link in the footer of any page on the Synthesis website; or
- Contacting Synthesis at [privacy@synthesis.ai].

Withdrawing consent stops the relevant cookies from being set on future page loads. Previously set cookies can be deleted by clearing your browser's cookie storage (see Section 6).

### 5.4 Consent records

Synthesis records the following information when you provide cookie consent, for audit and compliance purposes:

- Timestamp of the consent event
- The consent choices made (accepted / rejected / specific categories)
- The version of the Cookie Policy in force at the time of consent
- An anonymous session identifier (not your email address)

This record is stored for a maximum of **3 years** from the date of consent, in accordance with GDPR accountability requirements. It is not linked to your email address unless you subsequently create an account.

---

## 6. Managing Cookies Through Your Browser

In addition to the consent controls on the Synthesis website, you can control cookies directly through your browser settings. The following links provide instructions for the most common browsers:

- **Google Chrome:** chrome://settings/cookies
- **Mozilla Firefox:** about:preferences#privacy
- **Safari (macOS):** Preferences → Privacy → Manage Website Data
- **Safari (iOS):** Settings → Safari → Privacy & Security
- **Microsoft Edge:** edge://settings/privacy
- **Opera:** Settings → Advanced → Privacy and Security

**Important:** Blocking or deleting strictly necessary cookies (Section 4.1) will impair or completely prevent access to authenticated areas of the Synthesis platform, including the dashboard, report history, and active ticker management.

Blocking analytics and error monitoring cookies will not affect your ability to use any feature of the Service.

### 6.1 Browser-level opt-out mechanisms

Synthesis honours the following browser-level privacy signals:

- **Do Not Track (DNT):** If your browser sends a DNT signal, Synthesis will treat this as a preference not to be tracked by analytics cookies, and PostHog analytics will not be activated for your session, regardless of your cookie banner choice.
- **Global Privacy Control (GPC):** Synthesis recognises GPC signals as a valid expression of your preference not to have your data sold or shared. As Synthesis does not sell or share data, the GPC signal has no additional material effect beyond the DNT treatment described above.

---

## 7. Third-Party Websites and Links

The Synthesis website may contain links to third-party websites (for example, links to SEC EDGAR, financial data sources, or support resources). This Cookie Policy applies only to the Synthesis website and platform. Synthesis is not responsible for the cookies or privacy practices of third-party websites. We encourage you to review the privacy and cookie policies of any third-party site you visit.

---

## 8. Cookies in Emails

Synthesis sends transactional emails (order confirmations, report delivery notifications, access codes, renewal reminders) and, where applicable, marketing emails (post-purchase sequences) via Resend. These emails may contain a single-pixel tracking image (an email open tracker) that records whether the email has been opened and the approximate geographic region of the device that opened it (derived from IP address).

**Email open tracking is used solely to:**
- Determine whether transactional emails have been successfully delivered and opened (for operational purposes, e.g., to re-send a failed delivery notification); and
- Measure the effectiveness of post-purchase email sequences at an aggregate level.

Email open tracking is **not** used for advertising profiling or cross-site tracking.

You may disable email open tracking by configuring your email client to block remote image loading. This is available in most major email clients (Outlook, Apple Mail, Gmail). Disabling image loading will not affect your ability to read emails or use any feature of the Service.

---

## 9. Changes to This Cookie Policy

Synthesis may update this Cookie Policy to reflect changes in the cookies used, applicable law, or the structure of the Service. In the event of material changes, Synthesis will notify registered users by email at their registered address and will reset the cookie consent banner so that all users are presented with the updated choices.

The version of this Policy applicable to your consent preferences is the version in force at the time you provided consent. A record of all published versions of this Policy will be maintained and made available upon request.

---

## 10. Contact

For any questions or complaints relating to this Cookie Policy or Synthesis's use of cookies:

**Synthesis — Data Protection**
[FULL LEGAL NAME OF OPERATOR]
[ADDRESS], Spain

Email: **[privacy@synthesis.ai]**

You also have the right to lodge a complaint with the competent data protection supervisory authority:

**Agencia Española de Protección de Datos (AEPD)**
C/ Jorge Juan, 6, 28001 Madrid, Spain
Website: www.aepd.es

---

## Appendix — Cookie Inventory Summary

The following table provides a complete, at-a-glance inventory of all cookies that may be set by Synthesis, for ease of reference and regulatory audit purposes.

| Cookie name | Category | Provider | Personal data? | Consent required? | Duration |
|---|---|---|---|---|---|
| `sb-[ref]-auth-token` | Strictly necessary | Supabase | Session token (pseudonymous) | No | 7 days / Session |
| `sb-[ref]-auth-token-code-verifier` | Strictly necessary | Supabase | No | No | Session |
| `__Host-next-auth.csrf-token` | Strictly necessary | Next.js | No | No | Session |
| `__Secure-next-auth.callback-url` | Strictly necessary | Next.js | No | No | Session |
| `ph_[key]_posthog` | Analytics | PostHog EU | No (anonymous ID only) | Yes | 1 year |
| `ph_[key]_posthog_ses` | Analytics | PostHog EU | No | Yes | 30 min |
| `__ph_opt_in_out_[key]` | Analytics | PostHog EU | No | Yes | 1 year |
| `sentry-sc` | Error monitoring | Sentry | No (anonymous session ID) | Yes | Session |

**Total cookie count: 8**
**Cookies requiring consent: 4 (analytics + error monitoring)**
**Cookies not requiring consent: 4 (strictly necessary)**
**Advertising cookies: 0**
**Third-party tracking cookies: 0**

---

*End of Cookie Policy — Synthesis v1.0*
*[DATE TO BE INSERTED UPON PUBLICATION]*

---

> **Implementation notes for the development team:**
>
> - The cookie consent banner must be implemented using a compliant Consent Management Platform (CMP) or a custom implementation that satisfies the requirements of Section 5. Recommended open-source option: **Cookiebot** (commercial) or a custom React component backed by PostHog's built-in consent management. The banner must be the first element rendered on every public page before any non-essential scripts are loaded.
> - PostHog must be initialised in `opt_out_capturing_by_default: true` mode so that no analytics data is captured until the user affirmatively accepts analytics cookies. On acceptance, call `posthog.opt_in_capturing()`.
> - Sentry must similarly be initialised with session tracking disabled by default and only enabled upon user consent.
> - The "Cookie Settings" link in the website footer must open the consent preference centre (re-opening the cookie banner or a dedicated modal) and must be present on every page of the website, including authenticated dashboard pages.
> - The `__Host-` prefix on the CSRF cookie name enforces that it is only sent over HTTPS and is bound to the exact host (not a subdomain). This is a security hardening measure, not cosmetic.
> - The DNT and GPC signal detection must be implemented server-side (reading the `Sec-GPC` and `DNT` request headers) and client-side (reading `navigator.doNotTrack` and `navigator.globalPrivacyControl`). If either signal is detected, PostHog must not be initialised, regardless of the stored consent preference.
> - Email open tracking (Section 8) must be reviewed at implementation: if Resend's standard transactional email includes an open tracking pixel by default, this must be disclosed as stated and must be configurable to be disabled per-user upon request.
> - Cookie names containing `[project-ref]` and `[api_key]` are dynamic and will be finalized during development. The Cookie Policy Appendix must be updated with the actual cookie names before launch.
