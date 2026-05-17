# Privacy Policy

**Synthesis — Automated Investment Analysis Platform**
**Effective date:** [DATE TO BE INSERTED UPON PUBLICATION]
**Last updated:** [DATE TO BE INSERTED UPON PUBLICATION]
**Version:** 1.0

---

## 1. Identity and Contact Details of the Data Controller

The data controller responsible for the processing of your personal data is:

**[FULL LEGAL NAME OF OPERATOR]**
Self-employed individual (autónomo)
Tax identification number: [NIF/NIE TO BE INSERTED]
Address: [ADDRESS TO BE INSERTED], Spain

Email (data protection enquiries): [privacy@synthesis.ai]
Email (general support): [support@synthesis.ai]

Synthesis is operated from Spain and is therefore subject to Regulation (EU) 2016/679 of the European Parliament and of the Council of 27 April 2016 (the **"General Data Protection Regulation"** or **"GDPR"**) as the primary applicable data protection framework. This framework is applied as a baseline standard to all users worldwide, regardless of their country of residence.

For users resident in Spain, the applicable national legislation complementing the GDPR is **Ley Orgánica 3/2018, de 5 de diciembre, de Protección de Datos Personales y garantía de los derechos digitales (LOPDGDD)**.

---

## 2. Scope and Purpose of This Policy

This Privacy Policy describes:

- What personal data Synthesis collects from you and how;
- The purposes for which that data is processed;
- The legal basis for each processing activity;
- How long your data is retained;
- Who your data is shared with and under what conditions;
- Your rights as a data subject and how to exercise them;
- How Synthesis handles international data transfers;
- Specific provisions applicable to users in certain jurisdictions.

This Policy applies to all users of the Synthesis platform, including visitors to the website, one-shot report purchasers, add-on subscribers, and subscription plan holders.

---

## 3. Data We Collect and How We Collect It

Synthesis operates on a principle of **data minimization**: we collect only the personal data that is strictly necessary to provide the Service, process payments, deliver reports, and communicate with you.

### 3.1 Data you provide directly

**When placing a one-shot order or configuring a report:**
- Email address
- Ticker symbol(s) requested (not personal data, but associated with your account)
- Preferred analysis tier (Core, Prime, Edge)
- Language preference

**When creating or accessing a registered account:**
- Email address
- Password (stored in hashed form via Supabase Auth — never stored in plaintext)
- Full name (optional, used only if voluntarily provided)

**When purchasing a subscription:**
- Email address
- Billing information (handled exclusively by Stripe — see Section 6.2)

**When contacting support:**
- Email address
- Any information you voluntarily include in your message

### 3.2 Data generated automatically by the Service

**Order and transaction data:**
- Order identifier and reference
- Product tier purchased
- Add-ons selected
- Purchase timestamp
- Payment status (confirmed, failed, refunded)
- Access code (stored as a bcrypt hash — the plaintext code is never retained after delivery)
- Access code expiry timestamp
- Terms and Conditions acceptance timestamp and version

**Account activity data:**
- Account creation date
- Last login timestamp
- Active ticker list (subscription users)
- Ticker swap history
- On-demand analysis request log

**Report delivery data:**
- Report generation status (queued, processing, delivered, failed)
- Report delivery timestamp
- Signed download URL access log (timestamp only — no content of the report is logged)
- Add-on status (active, exhausted, expired)

**Technical and operational data:**
- Error logs collected by Sentry (error monitoring) — see Section 6.5
- Product analytics events collected by PostHog (e.g., page views, funnel steps, feature usage) — see Section 6.6
- Server-side request logs (IP address, timestamp, HTTP method, response code) retained for security and debugging purposes

### 3.3 Data we do NOT collect

Synthesis does **not** collect:
- Payment card numbers, bank account details, or any other full payment credentials (these are processed exclusively by Stripe)
- Government-issued identification documents
- Social Security numbers or national identity numbers
- Biometric data
- Health or medical data
- Data concerning racial or ethnic origin, political opinions, religious beliefs, or sexual orientation
- Data from minors under the age of 18 (users must confirm they are of legal age at registration)

---

## 4. Purposes of Processing and Legal Bases

The table below describes each processing activity, its purpose, and the legal basis under which it is conducted pursuant to Article 6 GDPR.

| Processing activity | Purpose | Legal basis (Art. 6 GDPR) |
|---|---|---|
| Email address collection at checkout | Identify the purchaser, deliver the access code, and send the order confirmation | Art. 6(1)(b) — Performance of a contract |
| Order and transaction record creation | Fulfil the purchased service, track delivery status, manage add-ons and subscription renewals | Art. 6(1)(b) — Performance of a contract |
| Account creation (Supabase Auth) | Enable dashboard access, manage subscriptions, authenticate the user | Art. 6(1)(b) — Performance of a contract |
| Stripe customer record | Process payment, manage subscription billing, handle refund or chargeback requests | Art. 6(1)(b) — Performance of a contract |
| Terms acceptance logging (timestamp + version) | Demonstrate that the user accepted the applicable Terms at the time of purchase — legal traceability | Art. 6(1)(c) — Compliance with a legal obligation |
| Access code generation and storage (hashed) | Provide secure unauthenticated access to order status for one-shot purchasers | Art. 6(1)(b) — Performance of a contract |
| Post-purchase email sequences (marketing) | Send educational content, subscription upgrade offers, and renewal reminders to existing customers | Art. 6(1)(f) — Legitimate interests (customer retention and service improvement, subject to opt-out right) |
| News Pulse email digests | Deliver the news alert service purchased by the user | Art. 6(1)(b) — Performance of a contract |
| Earnings update notifications | Notify the user of automatic report updates triggered by earnings events | Art. 6(1)(b) — Performance of a contract |
| Renewal reminder emails | Inform the user of upcoming subscription renewals | Art. 6(1)(b) — Performance of a contract / Art. 6(1)(c) — Legal obligation (auto-renewal disclosure) |
| Sentry error monitoring | Identify and diagnose technical errors to maintain service quality and security | Art. 6(1)(f) — Legitimate interests (service reliability and security) |
| PostHog product analytics | Understand user behaviour at an aggregated and anonymized level to improve the Service | Art. 6(1)(f) — Legitimate interests (product improvement), subject to cookie consent where applicable |
| Server-side request logs | Security monitoring, abuse detection, and technical debugging | Art. 6(1)(f) — Legitimate interests (security and fraud prevention) |
| AI pipeline execution logs (agent_runs table) | Internal cost monitoring, quality assurance, and system debugging — not exposed to users | Art. 6(1)(f) — Legitimate interests (operational management) — these logs do not contain personal data beyond the ticker and order reference |
| Support communication records | Respond to user enquiries and resolve disputes | Art. 6(1)(b) — Performance of a contract / Art. 6(1)(f) — Legitimate interests (dispute resolution) |

### Note on legitimate interests

Where processing is based on Article 6(1)(f) (legitimate interests), Synthesis has assessed that its legitimate interests are not overridden by the interests, rights, or freedoms of data subjects, having regard to the nature of the data processed, the reasonable expectations of users, and the availability of opt-out mechanisms where applicable. You may request a copy of this balancing assessment by contacting [privacy@synthesis.ai].

---

## 5. Data We Do Not Use for AI Training

**Synthesis does not use any personal data — including your email address, purchase history, ticker requests, or any content of generated reports — to train, fine-tune, or otherwise improve any AI or machine learning model operated by Synthesis or any third party.**

The AI models used by Synthesis (Claude, operated by Anthropic) are accessed via API and are governed by Anthropic's own terms of service and privacy policy. Synthesis does not pass any user personal data to Anthropic's API beyond what is technically necessary to generate the requested report (specifically, the ticker symbol and the analysis tier). No email addresses, names, or account identifiers are transmitted to Anthropic.

Synthesis may use **aggregated, fully anonymized, and non-attributable** operational metrics (such as report generation time, quality score distributions, and agent error rates) for internal system improvement purposes. Such data cannot be used to identify any individual user or any specific ticker request made by a user.

---

## 6. Third-Party Data Processors and Sub-processors

Synthesis uses the following third-party services that process personal data on our behalf as data processors. Each has been selected on the basis of providing adequate data protection guarantees.

### 6.1 Supabase (Supabase Inc., USA)

**Role:** Database and authentication platform.
**Data processed:** User accounts (email, hashed password, account type, creation date), order records, subscription records, active ticker lists, report metadata.
**Data location:** EU region (where selected during project setup — confirm at implementation). Supabase offers EU-hosted instances.
**Safeguards:** Standard Contractual Clauses (SCCs) for transfers to the USA where applicable. Supabase's Data Processing Agreement is available at supabase.com/privacy.

> **Implementation note:** The Supabase project must be configured to use the EU (Frankfurt) region to minimize data transfer complexity. Verify this setting before launch.

### 6.2 Stripe (Stripe Payments Europe, Ltd., Ireland)

**Role:** Payment processing.
**Data processed:** Email address (as customer identifier), payment card data (processed and stored exclusively by Stripe — Synthesis never receives or stores full card details), billing history, subscription status.
**Data location:** Stripe Payments Europe, Ltd. is an Irish entity subject to EU data protection law. Card data is processed under PCI-DSS Level 1 certification.
**Safeguards:** Stripe's Data Processing Agreement and Privacy Policy are available at stripe.com/privacy. Stripe is certified under the EU-US Data Privacy Framework.

### 6.3 Resend (Resend Inc., USA)

**Role:** Transactional email delivery and marketing email sequences.
**Data processed:** Recipient email address, email content (order confirmations, access codes, report delivery notifications, post-purchase sequences, News Pulse digests).
**Data location:** USA. Transfers are governed by Standard Contractual Clauses.
**Safeguards:** Resend's Data Processing Agreement is available at resend.com/legal/dpa.

### 6.4 Cloudflare R2 (Cloudflare, Inc., USA)

**Role:** Secure file storage for generated reports (PDF and PPTX files).
**Data processed:** Generated report files (stored under a path keyed by user ID and order ID — the files themselves contain the ticker and analysis content but are not directly attributable to personal identifiers without access to the Supabase database). Signed URL access logs.
**Data location:** USA (Cloudflare's global network with storage in the USA). Transfers are governed by Standard Contractual Clauses.
**Safeguards:** Cloudflare's Data Processing Addendum is available at cloudflare.com/privacypolicy. Cloudflare is certified under the EU-US Data Privacy Framework.

> **Implementation note:** Generated reports contain no personal data of the user beyond what may appear in the disclaimer block (operator name). However, the file path structure includes the user_id, which is an indirect personal data point. Files must be access-controlled and never publicly listed.

### 6.5 Sentry (Functional Software, Inc. dba Sentry, USA)

**Role:** Error monitoring and performance tracking.
**Data processed:** Error stack traces, page URLs at the time of error, browser and device type, and — if a user is authenticated at the time of an error — the user ID (not email). Sentry is configured to **scrub** email addresses and other PII from error payloads before transmission.
**Data location:** USA. Transfers are governed by Standard Contractual Clauses.
**Safeguards:** Sentry's Data Processing Addendum is available at sentry.io/legal/dpa.

> **Implementation note:** Sentry must be configured with `beforeSend` hooks to strip any PII (email addresses, access codes, full names) from error payloads. This is a mandatory security configuration, not optional.

### 6.6 PostHog (PostHog Inc., USA)

**Role:** Product analytics and funnel tracking.
**Data processed:** Page views, click events, funnel step completions (e.g., "checkout initiated", "report delivered"), session duration, device and browser type. PostHog is configured to **not** collect email addresses or account identifiers in analytics events beyond an anonymized session ID.
**Data location:** PostHog offers EU-hosted instances (EU Cloud). Synthesis will use the EU Cloud instance.
**Safeguards:** PostHog's Data Processing Agreement is available at posthog.com/dpa. EU Cloud instance is hosted in Frankfurt.

> **Implementation note:** PostHog must be deployed in EU Cloud mode. Identify calls that associate analytics events with a user ID must be reviewed to ensure compliance. IP anonymization must be enabled.

### 6.7 Anthropic (Anthropic, PBC, USA)

**Role:** AI model provider (Claude — all agent models).
**Data processed:** The ticker symbol and analysis tier are transmitted to the Anthropic API as part of each report generation request. No email addresses, user IDs, or other personal identifiers are transmitted. The AI pipeline processes publicly available financial data, not user personal data.
**Data location:** USA. Anthropic processes API requests in the USA.
**Safeguards:** Anthropic's API usage policy and privacy notice are available at anthropic.com/privacy. Synthesis's use of the API is governed by Anthropic's API Terms of Service, which include data processing provisions. API inputs and outputs are not used by Anthropic to train models under the current commercial API terms.

### 6.8 Financial Modeling Prep (FMP) and Tavily

**Role:** Financial data provider (FMP) and web search provider (Tavily), used during report generation.
**Data processed:** Ticker symbols only. No personal data is transmitted to these services.
**Note:** These services are not personal data processors within the meaning of the GDPR. No data processing agreement is required.

### 6.9 n8n (n8n GmbH, Germany)

**Role:** Workflow automation and AI pipeline orchestration.
**Data processed:** Order identifiers, ticker symbols, report generation status. n8n Cloud is used. No email addresses or user personal data are transmitted to n8n workflows beyond what is necessary to route the report generation task (order ID and ticker).
**Data location:** n8n GmbH is a German entity subject to EU data protection law. n8n Cloud is hosted in the EU.
**Safeguards:** n8n's Data Processing Agreement is available at n8n.io/legal.

---

## 7. Data Retention

Synthesis retains personal data for the minimum period necessary for the purpose for which it was collected, subject to any overriding legal retention obligations.

| Data category | Retention period | Basis for retention |
|---|---|---|
| User account data (email, account type) | Duration of the account + 3 years after last activity or account deletion request | Contractual relationship + legitimate interest in dispute resolution |
| Order records (including terms acceptance log) | 5 years from the date of the transaction | Legal obligation (Spanish and EU commercial and tax record-keeping requirements) |
| Subscription records | Duration of the subscription + 5 years | Legal obligation |
| Stripe payment records | As determined by Stripe's own retention policy (typically 7 years for tax purposes) | Legal obligation (Stripe as data controller of payment data) |
| Generated report files (Cloudflare R2) | 90 days from the date of delivery, after which they are permanently deleted | Minimization — the User has had ample time to download the report |
| Access codes (hashed) | 30 days from the order date (automatically expires) | Functional necessity |
| Post-purchase email sequence eligibility | Until the User unsubscribes or the sequence completes | Legitimate interest / contract |
| Sentry error logs | 90 days (Sentry's default retention on the applicable plan) | Operational necessity |
| PostHog analytics events | 12 months | Product improvement |
| Server-side request logs | 30 days | Security monitoring |
| Support correspondence | 3 years from the date of the last communication | Legitimate interest (dispute resolution) |

Upon expiry of the applicable retention period, personal data is either permanently deleted or irreversibly anonymized. Anonymized data (which is no longer personal data within the meaning of the GDPR) may be retained indefinitely for statistical purposes.

---

## 8. Your Rights as a Data Subject

Under the GDPR and applicable national data protection law, you have the following rights in relation to your personal data processed by Synthesis. These rights apply to all users worldwide to whom the GDPR applies, and Synthesis extends equivalent rights, to the extent practicable, to all other users.

### 8.1 Right of access (Art. 15 GDPR)

You have the right to obtain confirmation of whether Synthesis processes personal data about you and, if so, to receive a copy of that data together with information about the purposes of processing, the categories of data, the recipients, the retention periods, and the existence of your other rights.

### 8.2 Right to rectification (Art. 16 GDPR)

You have the right to request the correction of inaccurate personal data and the completion of incomplete personal data held by Synthesis.

### 8.3 Right to erasure / Right to be forgotten (Art. 17 GDPR)

You have the right to request the deletion of your personal data where:
- The data is no longer necessary for the purposes for which it was collected;
- You withdraw consent (where processing is based on consent);
- You object to processing based on legitimate interests and there are no overriding legitimate grounds;
- The data has been unlawfully processed.

This right is subject to exceptions, including where retention is required by law (e.g., tax record-keeping obligations applicable to transaction data). Where erasure of certain data is not possible due to legal retention obligations, Synthesis will inform you of the applicable exception and will restrict processing of the data to the extent possible.

### 8.4 Right to restriction of processing (Art. 18 GDPR)

You have the right to request that Synthesis restricts the processing of your personal data in certain circumstances, including where you contest the accuracy of the data, where processing is unlawful, or where you have objected to processing pending verification of legitimate grounds.

### 8.5 Right to data portability (Art. 20 GDPR)

Where processing is based on your consent or on the performance of a contract, and is carried out by automated means, you have the right to receive your personal data in a structured, commonly used, and machine-readable format and to transmit that data to another controller.

### 8.6 Right to object (Art. 21 GDPR)

You have the right to object at any time to the processing of your personal data where that processing is based on legitimate interests (Art. 6(1)(f)), including profiling. Synthesis will cease the processing unless it can demonstrate compelling legitimate grounds that override your interests, rights, and freedoms, or the processing is necessary for the establishment, exercise, or defence of legal claims.

**Specific right to object to direct marketing:** You have an unconditional right to object to the processing of your personal data for direct marketing purposes (including the post-purchase email sequences described in this Policy). You may exercise this right at any time by clicking the unsubscribe link included in every marketing email or by contacting [privacy@synthesis.ai].

### 8.7 Rights in relation to automated decision-making (Art. 22 GDPR)

Synthesis does not make decisions that produce legal effects or similarly significant effects on individuals solely by automated means. The reports generated by Synthesis are not decisions — they are analytical information documents. No automated decision-making within the meaning of Article 22 GDPR takes place.

### 8.8 Right to withdraw consent

Where processing is based on your consent, you have the right to withdraw that consent at any time without affecting the lawfulness of processing based on consent before its withdrawal.

### 8.9 How to exercise your rights

To exercise any of the above rights, contact Synthesis at:
**[privacy@synthesis.ai]**

Please include in your request: your full name (if provided at registration), the email address associated with your account, and a description of the right you wish to exercise. Synthesis will respond within **30 days** of receiving your request. Where requests are complex or numerous, this period may be extended by a further 60 days, in which case you will be notified within the initial 30-day period.

Synthesis will not charge a fee for the exercise of your rights unless requests are manifestly unfounded or excessive, in which case a reasonable fee reflecting administrative costs may be charged, or Synthesis may refuse to act on the request (with notification of the reason).

Synthesis may request proof of identity before processing a rights request to prevent unauthorized disclosure of personal data.

### 8.10 Right to lodge a complaint with a supervisory authority

Without prejudice to any other administrative or judicial remedy, you have the right to lodge a complaint with a data protection supervisory authority if you believe that the processing of your personal data infringes the GDPR or applicable national data protection law.

The competent supervisory authority for Synthesis, as a controller established in Spain, is:

**Agencia Española de Protección de Datos (AEPD)**
C/ Jorge Juan, 6, 28001 Madrid, Spain
Website: www.aepd.es
Phone: +34 901 100 099

Users resident in another EU/EEA Member State may also lodge a complaint with the supervisory authority of their country of residence.

---

## 9. International Data Transfers

Synthesis is established in Spain (EU) and processes data under the GDPR. Some of the third-party processors listed in Section 6 are located outside the European Economic Area (EEA), in particular in the United States.

Where personal data is transferred to third countries (countries outside the EEA that have not received an adequacy decision from the European Commission), Synthesis ensures that appropriate safeguards are in place in accordance with Chapter V of the GDPR. The primary safeguard used for transfers to the USA is the **Standard Contractual Clauses (SCCs)** adopted by the European Commission (Decision 2021/914 of 4 June 2021), supplemented where necessary by a Transfer Impact Assessment.

Where a third-party processor is certified under the **EU-US Data Privacy Framework** (adequacy decision of 10 July 2023), that certification provides an additional ground for lawful transfer.

The following table summarizes the transfer mechanism applicable to each non-EEA processor:

| Processor | Country | Transfer mechanism |
|---|---|---|
| Supabase | USA (EU region option used) | EU region hosting — no transfer if EU region selected |
| Stripe | Ireland (EU) | No transfer — EU entity |
| Resend | USA | Standard Contractual Clauses |
| Cloudflare R2 | USA | Standard Contractual Clauses + EU-US Data Privacy Framework |
| Sentry | USA | Standard Contractual Clauses + EU-US Data Privacy Framework |
| PostHog | USA (EU Cloud used) | EU Cloud hosting — no transfer if EU instance selected |
| Anthropic | USA | Standard Contractual Clauses (API data processing terms) |
| n8n | Germany (EU) | No transfer — EU entity |

Synthesis will update this table if the processing locations or transfer mechanisms of any processor change.

---

## 10. Cookies and Tracking Technologies

### 10.1 What cookies Synthesis uses

Synthesis uses the minimum number of cookies necessary to operate the Service. The following categories of cookies may be set:

| Category | Cookie name / provider | Purpose | Duration |
|---|---|---|---|
| **Strictly necessary** | Supabase Auth session token | Maintains your authenticated session in the dashboard | Session / 7 days if "remember me" is selected |
| **Strictly necessary** | CSRF protection token | Prevents cross-site request forgery attacks | Session |
| **Analytics** | PostHog (ph_*) | Anonymous product analytics — page views, funnel steps | 1 year |
| **Error monitoring** | Sentry session ID | Links error reports to an anonymous session for debugging | Session |

Synthesis does **not** use:
- Advertising or targeting cookies
- Third-party social media tracking pixels
- Fingerprinting technologies

### 10.2 Cookie consent

Strictly necessary cookies are exempt from consent requirements under applicable EU law (ePrivacy Directive and its national transpositions). They are set automatically as they are essential for the Service to function.

Analytics cookies (PostHog) and error monitoring cookies (Sentry session ID) are set on the basis of your consent, which is requested via the cookie consent banner displayed on your first visit to the Synthesis website. You may withdraw your consent at any time by adjusting your cookie preferences through the cookie settings accessible in the website footer.

Refusing analytics cookies does not affect your ability to use the Service.

### 10.3 Managing cookies

You may control and delete cookies through your browser settings. Please note that disabling strictly necessary cookies will impair the functionality of the dashboard and authenticated areas of the Service.

---

## 11. Security of Personal Data

Synthesis implements appropriate technical and organizational measures to protect personal data against unauthorized access, disclosure, alteration, or destruction, in accordance with Article 32 GDPR. These measures include:

- **Authentication:** Passwords are stored exclusively as bcrypt hashes (never in plaintext). Supabase Auth handles all authentication flows.
- **Access codes:** One-shot access codes are stored as bcrypt hashes. The plaintext code is transmitted once (via email) and is never stored.
- **Database security:** Row-Level Security (RLS) policies are enforced at the database layer in Supabase, ensuring that users can only access their own data.
- **Data in transit:** All communications between the user's browser and the Synthesis platform are encrypted using TLS (HTTPS). All communications between Synthesis's backend and third-party processors are similarly encrypted.
- **Data at rest:** Report files stored in Cloudflare R2 are encrypted at rest. Database data in Supabase is encrypted at rest.
- **Access control:** The Supabase service role key (which bypasses RLS) is stored exclusively as a server-side environment variable and is never exposed to the client or included in frontend code.
- **API security:** Webhook endpoints (Stripe, n8n) are protected by signature verification mechanisms. Rate limiting is applied to API routes.
- **Monitoring:** Sentry provides real-time error monitoring. Unusual activity patterns trigger alerts.
- **No session persistence on one-shot flows:** One-shot users who do not log in to a registered account use a code-based access mechanism with no persistent session cookies.

Notwithstanding the above, no data transmission over the internet or electronic storage system can be guaranteed to be 100% secure. In the event of a personal data breach that is likely to result in a risk to the rights and freedoms of individuals, Synthesis will notify the competent supervisory authority (AEPD) within 72 hours of becoming aware of the breach, and will notify affected users without undue delay where the breach is likely to result in a high risk to their rights and freedoms, in accordance with Articles 33 and 34 GDPR.

---

## 12. Children's Privacy

The Service is not directed at children under the age of 18. Synthesis does not knowingly collect personal data from individuals under 18 years of age. Users must confirm at registration that they are of legal age (at least 18 years old) in their jurisdiction. If Synthesis becomes aware that personal data has been collected from a child under 18 without appropriate parental consent, it will take immediate steps to delete that data. If you believe a child under 18 has provided personal data to Synthesis, please contact [privacy@synthesis.ai].

---

## 13. Jurisdiction-Specific Provisions

### 13.1 California (USA) — California Consumer Privacy Act (CCPA) / California Privacy Rights Act (CPRA)

California residents have the following rights under the CCPA/CPRA, in addition to the rights described in Section 8:

- **Right to know:** The right to know what personal information Synthesis collects, uses, discloses, and sells.
- **Right to delete:** The right to request deletion of personal information collected from you, subject to certain exceptions.
- **Right to correct:** The right to request correction of inaccurate personal information.
- **Right to opt out of sale or sharing:** Synthesis does **not** sell or share personal information for cross-context behavioral advertising within the meaning of the CCPA/CPRA.
- **Right to limit use of sensitive personal information:** Synthesis does not process sensitive personal information (as defined by the CCPA) beyond what is strictly necessary to provide the Service.
- **Right to non-discrimination:** Synthesis will not discriminate against you for exercising any of your CCPA/CPRA rights.

To exercise your CCPA/CPRA rights, contact [privacy@synthesis.ai]. Synthesis will respond within 45 days (extendable by a further 45 days with notice where necessary).

**Do Not Sell or Share My Personal Information:** As noted above, Synthesis does not sell or share personal information. No opt-out mechanism is therefore required, but Synthesis honors browser-level Global Privacy Control (GPC) signals as a signal of the user's preference.

**Categories of personal information collected:** Identifiers (email address), commercial information (purchase history), internet or electronic network activity (usage data, error logs).

**Purposes of collection:** To provide the Service, process payments, communicate with you, and improve the platform.

### 13.2 United Kingdom — UK GDPR

Following the UK's departure from the EU, the GDPR has been retained in UK law as the UK GDPR. Synthesis's processing of data relating to UK residents is governed by the UK GDPR and the Data Protection Act 2018. The rights described in Section 8 apply equally to UK residents. Complaints may be lodged with the Information Commissioner's Office (ICO) at ico.org.uk.

### 13.3 Brazil — Lei Geral de Proteção de Dados (LGPD)

Brazilian residents have rights under the LGPD that are broadly equivalent to those described in Section 8. The legal bases for processing described in Section 4 correspond to the legal hypotheses set out in Article 7 of the LGPD. Complaints may be lodged with the Autoridade Nacional de Proteção de Dados (ANPD).

### 13.4 Canada — Personal Information Protection and Electronic Documents Act (PIPEDA) / provincial equivalents

Canadian residents have rights under PIPEDA (or applicable provincial privacy legislation) that are broadly equivalent to those described in Section 8. Synthesis's processing of Canadian residents' data is conducted on the basis of express consent (at the point of purchase) or implied consent (for strictly operational processing). Complaints may be lodged with the Office of the Privacy Commissioner of Canada.

### 13.5 Other jurisdictions

Users resident in jurisdictions not specifically addressed in this Section retain the rights described in Section 8, which are applied by Synthesis as a global baseline standard. Where local law provides additional rights, Synthesis will seek to honour those rights to the extent practicable.

---

## 14. Changes to This Privacy Policy

Synthesis may update this Privacy Policy from time to time to reflect changes in processing activities, applicable law, or the structure of the Service. In the event of material changes, Synthesis will notify registered users by email at their registered address no fewer than **15 days** before the changes take effect.

The version of this Policy applicable to any given processing activity is the version in force at the time the activity was initiated. A record of all published versions of this Policy will be maintained and made available upon request.

Your continued use of the Service after the effective date of an updated Policy constitutes your acknowledgment of the changes. If you do not accept the changes, you must discontinue your use of the Service and may request deletion of your account and data in accordance with Section 8.3.

---

## 15. Contact and Data Protection Enquiries

For any questions, requests, or complaints relating to this Privacy Policy or the processing of your personal data, please contact:

**Synthesis — Data Protection**
[FULL LEGAL NAME OF OPERATOR]
[ADDRESS], Spain

Email: **[privacy@synthesis.ai]**

Synthesis aims to respond to all data protection enquiries within **30 days**. For urgent matters involving potential data breaches or security incidents, please include "URGENT — DATA BREACH" in the subject line of your email.

---

## Appendix A — Data Flow Summary (for transparency)

The following describes the complete journey of your personal data through the Synthesis system, from the moment you place an order to the moment your data is deleted.

**Step 1 — Order placement**
You enter your email address and ticker on the Synthesis website. This data is transmitted over HTTPS to the Synthesis API (hosted on Vercel, USA/EU). The email is stored in Supabase (EU region). The ticker is not personal data.

**Step 2 — Payment**
You are redirected to Stripe Checkout. Synthesis never sees your card details. Stripe processes the payment and notifies Synthesis via a signed webhook. Stripe stores your payment data according to its own retention policy.

**Step 3 — Account creation**
If you are a new user, Supabase Auth creates a hashed-password account linked to your email. A temporary password is sent via Resend to your email address.

**Step 4 — Report generation**
Synthesis triggers an n8n workflow. The workflow passes the ticker symbol and tier to the Anthropic Claude API. No personal data is passed to Anthropic. Financial data is fetched from FMP and Tavily (ticker only). The generated report is stored in Cloudflare R2 under a path keyed by your user ID and order ID.

**Step 5 — Delivery**
Resend sends you an email containing a signed download link (valid for 24 hours) and your access code. The access code is stored as a bcrypt hash in Supabase. The download link expires automatically.

**Step 6 — Post-purchase communications**
If you have not unsubscribed, n8n workflows trigger post-purchase email sequences via Resend over the following days and weeks.

**Step 7 — Data retention and deletion**
Report files in Cloudflare R2 are deleted 90 days after delivery. Account data is retained for 3 years after your last activity. Transaction records are retained for 5 years for legal compliance. You may request earlier deletion in accordance with Section 8.3.

---

## Appendix B — Glossary

- **Controller:** The entity that determines the purposes and means of processing personal data. Synthesis is the controller for data processed under this Policy.
- **Processor:** An entity that processes personal data on behalf of the controller. The third-party services listed in Section 6 are processors.
- **Personal data:** Any information relating to an identified or identifiable natural person.
- **Processing:** Any operation performed on personal data, including collection, storage, use, disclosure, and deletion.
- **GDPR:** General Data Protection Regulation (EU) 2016/679.
- **LOPDGDD:** Ley Orgánica 3/2018, de Protección de Datos Personales y garantía de los derechos digitales (Spain).
- **SCCs:** Standard Contractual Clauses — contractual mechanisms approved by the European Commission for international data transfers.
- **RLS:** Row-Level Security — a database-level access control mechanism implemented in Supabase/PostgreSQL.
- **bcrypt:** A cryptographic hash function used to store passwords and access codes securely, such that the original value cannot be recovered.

---

*End of Privacy Policy — Synthesis v1.0*
*[DATE TO BE INSERTED UPON PUBLICATION]*

---

> **Implementation notes for the development team:**
>
> - The Privacy Policy must be accessible from the website footer on all pages, at all times, without requiring login.
> - The cookie consent banner must be implemented before any non-essential cookies (PostHog analytics, Sentry session) are set. Strictly necessary cookies (Supabase Auth session, CSRF token) may be set without consent.
> - PostHog **must** be deployed using the EU Cloud instance (eu.posthog.com). IP anonymization must be enabled in the PostHog configuration.
> - Sentry **must** be configured with a `beforeSend` hook that scrubs email addresses, access codes, and any other PII from error payloads before transmission.
> - Supabase **must** be configured to use the EU (Frankfurt) region. Document this in the deployment checklist.
> - The `orders` table must include a `terms_version` field (TEXT) and a `terms_accepted_at` field (TIMESTAMPTZ) — already specified in the T&C implementation notes. The privacy policy version accepted must also be logged: add `privacy_version` (TEXT) field to the `orders` table.
> - Report files in Cloudflare R2 must have an automated deletion lifecycle rule set to 90 days. Configure this in the R2 bucket lifecycle settings.
> - The unsubscribe mechanism in all marketing emails must be a single-click action that immediately removes the user from all non-transactional sequences. It must not require login to action.
> - The data deletion request flow should be documented internally: upon receiving a deletion request to [privacy@synthesis.ai], the operator must: (1) verify identity, (2) delete the Supabase Auth user (which cascades via ON DELETE CASCADE), (3) request deletion of Stripe customer data, (4) delete R2 files, (5) confirm deletion to the user in writing. Transaction records subject to legal retention must be flagged as "restricted" rather than deleted.
