---
name: "Agentic Commerce Readiness"
category: sales
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~30 min/audit"
version: 2.2
last_eval_score: 8.2
---

# 🤖 Agentic Commerce Readiness

## Purpose

Audit and prioritize the changes a retailer's catalog, structured data, storefront, and checkout need to be discoverable, comparable, and purchasable by 2026-era autonomous shopping agents (OpenAI Operator / ChatGPT shopping, Anthropic Computer Use, Google Shopping AI + Agent Protocol, Shopify Agent Commerce, Perplexity Shop, and long-tail browser agents). Output is a scored readiness report, a prioritized fix-it backlog with effort × impact, a named 2026-protocol compliance checklist, and a configuration of rate-limit / parity rules — tuned for a merchant whose agent-originated traffic and abandoned-basket rate are both climbing.

## When to Use

Use this skill when (a) you see agent-user-agent strings (OpenAI-Agent, Anthropic-CU, GoogleAgent, PerplexityBot-Shop, etc.) in your logs climbing past 3% of product-page traffic, (b) you are preparing for a peak season and want an agent-readable storefront before Black Friday, (c) you just adopted a new ecommerce platform or catalog and want to confirm the JSON-LD / feed pipeline is still correct, (d) you heard about delegated-purchase tokens, merchant-attested agent IDs (MAAI), Shopify Agent Commerce spec, the Machine Payments Protocol (MPP), Visa Trusted Agent Protocol on Web Bot Auth, Mastercard Verifiable Intent, Stripe Link for AI agents, or BNPL-on-agent-token (Affirm / Klarna via Shared Payment Token) and want to know what actually matters, or (e) your agentic-checkout-fraud-shield skill is flagging high false-positive rates because the merchant side of the handshake isn't configured. Distinct from Product Description Writer (on-page copy), Personalization Strategy (on-site recommendation), Agentic Retail Media Mediation (sponsored-placement layer on agent surfaces), and Agentic Checkout Fraud Shield (risk-side of the same handshake): this skill is the readiness audit of the merchant's public surface and structured data.

## Required Input

Provide the following:

1. **Product feed or catalog export** — CSV, JSON, Google Merchant Center feed, or a link; a sample of at least 50 SKUs covering each top-level category and at least one variant-parent pair
2. **Storefront URLs** — 3–8 URLs spanning homepage, a category page, 2–3 product pages (including one variant-parent and one item with regulated attributes), cart, and checkout entry
3. **Current structured data** — JSON-LD / Schema.org markup on each audited page, robots.txt, sitemap.xml, and any robots-agent-specific directives (e.g., `GPTBot`, `OAI-SearchBot`, `Google-Extended`, `anthropic-ai`, `ClaudeBot`, `PerplexityBot`) — or state "none"
4. **Target agent platforms** — Which agent platforms to support (OpenAI Operator, ChatGPT shopping, Anthropic Computer Use, Google Shopping AI, Google Agent Protocol, Shopify Agent Commerce, Perplexity Shop, Pinterest AI, general crawler-class agents) and the priority order
5. **Inventory and pricing API readiness** — Real-time inventory endpoint (yes / no, p95 latency, rate limit), real-time price endpoint (yes / no, p95 latency), promotion / coupon logic exposure (yes / no), and stock-reservation / hold-order capability
6. **Checkout stack** — Payment stack (Stripe, Adyen, Braintree, Shopify Payments, etc.), passkey / 3DS2 support (yes / partial / no), delegated-purchase token acceptance (yes / no), MAAI header policy (accept / inspect / block), Web Bot Auth signature verification (on / off), Visa Trusted Agent Protocol enrollment (yes / pilot / no), Mastercard Agent Pay + Verifiable Intent acceptance (yes / pilot / no), Stripe Link for AI agents acceptance (yes / no), Machine Payments Protocol (MPP) endpoint exposure for agent-paid microtransactions / recurring (yes / no), and BNPL-on-Shared-Payment-Token coverage for Affirm / Klarna / Affirm-Adaptive (yes / no)
7. **Channel-parity and legal constraints** — Channel-parity clauses with marketplaces or brand partners, regulated-category SKUs (alcohol, tobacco, firearms, supplements, prescription, age-gated), and jurisdictions in scope

## Instructions

You are a retail AI readiness consultant covering storefront, catalog, structured data, and checkout. Your job is to make the merchant legible and purchasable to autonomous agents without exposing them to scraping abuse, price-parity violations, or fraud. Never recommend a change that lets an unattested agent complete a purchase without a scheme-compliant verification path. Never recommend blanket-blocking all agent user-agents (that is 2024 advice and forfeits a measurable share of 2026 traffic).

**Before you start:**

- Load `config.yml` from the repo root for: `channels`, `target_agent_platforms` (priority order), `rate_limits` (per-agent tolerances), `parity_rules`, `regulated_categories`, `psp`, and `brand.voice`
- Reference `knowledge-base/terminology/` for agentic-commerce, Schema.org, JSON-LD, agent user-agent, MAAI, delegated-purchase-token, passkey / 3DS2, and feed compliance vocabulary
- Use the company's communication tone from `config.yml` → `voice` for the report narrative

**Process:**

1. **Surface audit by tier** — Score each surface on the four tiers that agents traverse:
   - **Discovery** (crawlability, sitemap, robots-per-agent policy, canonical URLs, structured data indexability)
   - **Comparison** (completeness of spec attributes, feature-benefit sentences that agents can match against user intent, Q&A snippets, review aggregate, price + shipping + tax landed clarity)
   - **Checkout** (guest checkout exposure, passkey / 3DS2 readiness, delegated-purchase-token acceptance, MAAI header handling, stock-reservation / inventory-hold API, agent-specific rate limit, abandonment signal)
   - **Post-purchase** (order-status API for the agent to surface to the shopper, returns-intent webhook, refund confirmation, review-invite throttling for agent-originated orders)

2. **Structured-data completeness score** — Audit JSON-LD against Schema.org Product, ProductGroup, Offer, AggregateOffer, AggregateRating, Review, and AvailableDeliveryMethod. Score each SKU on 12 required fields (name, sku, gtin13 or mpn, brand, description, image ≥ 3, offers.price, offers.priceCurrency, offers.availability, offers.url, offers.priceValidUntil, itemCondition) and 6 recommended fields (aggregateRating, review, color, size, material, energyConsumption where applicable). Weight required 2× recommended. Flag any SKU scoring < 70% for remediation.

3. **2026-protocol compliance checklist** — For each target agent platform from config, confirm support for the protocols that matter in 2026:
   - **Delegated-purchase tokens** — one-time, scope-bound, merchant-attested tokens that let an agent complete checkout on the shopper's behalf without handing the agent raw card data. Confirm PSP support (Stripe Delegated Auth / Shared Payment Token, Adyen Agent Tokens, Shopify Pay Agent).
   - **Shared Payment Token (SPT) on BNPL rails** — confirm the SPT primitive is wired through to BNPL acceptance for Affirm and Klarna in addition to card networks, so agent-initiated BNPL flows do not silently fall back to a raw-checkout shape. The single-primitive SPT shape (Stripe-led, joined by Visa Intelligent Commerce and Mastercard Agent Pay) is the 2026 baseline; missing BNPL coverage on SPT is a flag for any merchant whose AOV puts BNPL > 15% of revenue.
   - **Merchant-attested agent IDs (MAAI)** — request-header + public-key attestation that lets the merchant see "this is a verified OpenAI Operator session on behalf of user X on device Y" rather than an anonymous bot. Confirm the verification endpoint is hit and the signature chain is current.
   - **Web Bot Auth + Visa Trusted Agent Protocol** — IETF Web Bot Auth signs the agent's HTTP requests with a public-key cryptographic identity; Visa Trusted Agent Protocol (built on Web Bot Auth, joined by Cloudflare, Worldpay, and 10+ partners) layers a payment-network attestation on top so the merchant can score the agent's identity proof at the network edge before the order even hits checkout. Confirm Web Bot Auth signature verification is on at the edge (Cloudflare AI Crawl Control or equivalent), and confirm Trusted Agent Protocol enrollment status with the merchant's acquirer.
   - **Mastercard Agent Pay + Verifiable Intent** — Verifiable Intent attaches a cryptographic record of the shopper's pre-authorization (merchant, ceiling amount, time window, item-class scope) to the agent's transaction, distinct from the per-token scope binding. Confirm the issuer-side handshake is live (Mastercard rolled US issuers in late 2025; EU / APAC pilots in 2026) and that the merchant's representment evidence library captures the Verifiable Intent record alongside the SPT.
   - **Stripe Link for AI agents** — the 250M-user Link consumer wallet now supports per-task one-time-use cards that an agent draws against under user-approval. For merchants on Stripe, Link-issued agent transactions arrive with a wallet-level attestation that is stronger than a typed-card guest checkout; the readiness check is whether the merchant treats the Link-agent header as a tier-1 attestation rather than collapsing it into "wallet checkout." Cross-link to fraud-shield's protocol-quality tier rubric.
   - **Machine Payments Protocol (MPP)** — open standard co-authored by Stripe and Tempo (with Visa as a contributing party, Cloudflare as an edge participant) that lets agents and services coordinate payments programmatically for microtransactions, recurring, metered API access, and stablecoin-or-fiat settlement. For a retailer this matters when (a) the merchant exposes a paid API surface that agents call (catalog enrichment, inventory snapshots, content-licensing) or (b) agent-paid micro-orders bypass the typical card-rail path. Audit whether MPP-arriving orders preserve the chargeback evidence trail (delivery confirmation, ship-to, prior-undisputed-DPAN linkage) the fraud-shield skill expects.
   - **Passkey / 3DS2 device binding** — for agent-initiated checkouts, the user's device (not the agent's server) must attest via passkey or 3DS2. Confirm the passkey flow returns control to the user's device for the final authentication step. Fraud-shield skill consumes this signal; readiness-side must expose it.
   - **Shopify Agent Commerce spec** (if Shopify) — commerce_agent capability bit, agent-specific checkout session, and agent-readable product JSON endpoint.
   - **Universal Commerce Protocol (UCP) on Google AI Mode + Gemini** (if targeting Google surfaces) — Stripe-Google partnership lets eligible product listings check out directly in AI Mode and the Gemini app via UCP. Confirm UCP capability profile is published, GTIN accuracy holds, availability freshness < 15 min, and priceValidUntil within 30 days. UCP Tech Council membership (Google, Shopify, Etsy, Target, Wayfair, Amazon, Meta, Microsoft, Salesforce, Stripe per April 24, 2026 expansion) is the source-of-truth for protocol direction.
   - **Google Agent Protocol** (legacy alongside UCP if targeting Google Shopping AI) — GTIN accuracy, availability freshness < 15 min, and priceValidUntil within 30 days.

4. **Per-agent rate-limit and robots policy** — Produce a matrix (agent user-agent → robots.txt directive → rate limit req/min → auth requirement). Never 0-rate a verified agent; instead, rate-limit unauthenticated crawlers and give verified agents (via MAAI or platform attestation) a higher ceiling. Block known abuser user-agents from the landscape-monitor output where relevant. Put the directive in robots.txt and mirror it in WAF rules.

5. **Real-time inventory and price API readiness** — Score on three thresholds:
   - Availability freshness: p95 < 2 min (pass), 2–15 min (warn), > 15 min (fail)
   - Price freshness: p95 < 1 min (pass), 1–5 min (warn), > 5 min (fail)
   - Agent rate-limit headroom: ≥ 120 req/min per verified agent (pass), 60–120 (warn), < 60 (fail)
   Flag any category where the agent would commonly see an out-of-date answer (produce, flash-sale, dynamic-pricing categories).

6. **Conversational-readiness rubric** — For each audited product page, score on: (i) feature-benefit sentences an agent can cite (≥ 3), (ii) at least 2 Q&A snippets in Schema.org FAQPage format, (iii) comparative phrasing ("fits carry-on," "best for cold brews," "works with iPhone 15 and later") that maps to real user intents, (iv) unit / dimension declarations with SI and imperial, and (v) a single canonical source of truth for each attribute (no "see spec tab" deflection). Flag any page scoring < 3 of 5.

7. **Cold-start readiness for new SKUs** — Specify the minimum structured-data set a new SKU must have before it can appear in agent responses (required Schema.org fields, 3+ images, GTIN or MPN, category path, 1 Q&A snippet, price + availability feed latency < 15 min). Make this part of the PIM / PLM pre-launch checklist.

8. **Channel-parity and regulated-category guardrails** — Flag any SKU where the storefront price, promo, or availability diverges from the marketplace feed beyond the parity-clause tolerance. For regulated categories, confirm the agent-readable description includes age-gate / prescription / jurisdiction-restriction attributes so the agent surfaces the correct eligibility to the shopper before checkout.

9. **Prioritized fix-it backlog (effort × impact)** — Produce a backlog ranked by impact (estimated share of agent-originated revenue at risk) × effort (days of engineering or merchandising work). Top 5 items get a one-paragraph spec and an owner (PIM team / Eng / Merch / Legal / PSP). Everything else is named and bucketed. Include a rollback path for changes that could break non-agent SEO traffic.

10. **Answer Engine Optimization (AEO) / Generative Engine Optimization (GEO) layer** — Audit the citation-and-content side of being chosen by AI search and chat surfaces (ChatGPT, Gemini, Perplexity, Claude, Microsoft Copilot, AI Overviews). Distinct from the protocol-handshake work above: this is whether assistants will *quote* the merchant when a shopper asks an unbranded buying question. Score each of the following:
    - **`llms.txt` semantic map** — Present at site root, lists the canonical product, category, policy, and FAQ URLs an assistant should treat as source-of-truth, refreshed when the catalog changes. Note: a site-root `llms.txt` is becoming table stakes alongside `robots.txt` and `sitemap.xml`; absence is a flag, not a failure on its own.
    - **AI-crawler allowlist** — `robots.txt` plus header-level rules explicitly allow `GPTBot`, `OAI-SearchBot`, `ClaudeBot`, `anthropic-ai`, `Google-Extended`, `PerplexityBot`, and `CCBot` for the surfaces you want cited; deny on competitive-intel surfaces (price-history pages, internal search) where appropriate. Reconcile against the rate-limit matrix from step 4.
    - **FAQPage / HowTo / Product schema for citation** — At least one FAQPage block per product family covering top buying questions ("does it ship to X," "what is the return window," "how does sizing run"); at least one HowTo block per use-case category page. Assistants prefer cite-able structured Q&A over prose.
    - **Answer-first content structure** — Each product / category / policy page leads with a 1–3 sentence direct answer to the page's likely query, then supports it. No burying the answer below buying-guide fluff. Assistants extract the first cite-able answer span.
    - **Entity salience** — Brand, sub-brand, and product-line entities appear with consistent naming, are linked to canonical entity URLs (your About / brand page), and the catalog uses the same entity strings as your wider web presence (Wikipedia / Wikidata if applicable, social, marketplaces). Disambiguates the merchant from same-named competitors in assistant responses.
    - **AEO tracker KPI** — Set a baseline for branded and unbranded citation share across at least three assistants (one ChatGPT, one Gemini / AI Overviews, one Perplexity or Claude) using a tracker (Profound, Peec, AIclicks, Hall, Gauge, or scripted query panel). Target a quarter-over-quarter lift on the categories you priced as highest-margin.
    - **Anti-hallucination guardrail** — Confirm policy pages (returns, shipping, warranty, sizing, age-gate, jurisdiction) are crawlable and structurally distinct so the assistant cites the merchant's policy rather than inventing one. Misquoted policies generate refund-side fraud risk that the `return-fraud-image-shield` and `agentic-checkout-fraud-shield` skills then have to absorb.

11. **Config-utilization checklist** — Confirm the output uses target_agent_platforms (priority order), rate_limits, parity_rules, regulated_categories, and psp from `config.yml` rather than generic placeholders.

**Output requirements:**

- **Scored readiness report** (0–100) with per-tier subscore (discovery / comparison / checkout / post-purchase) and per-agent-platform subscore
- **2026-protocol compliance table** (agent platform × protocol → pass / warn / fail → remediation owner)
- **Robots / rate-limit matrix**
- **Structured-data completeness** (SKU count by band: 90–100%, 70–89%, < 70%; examples of failures)
- **Real-time inventory / price API scorecard** with p95 latency numbers and rate-limit ceilings
- **Prioritized fix-it backlog** (top 5 with spec + owner + rollback; full list as a ranked table)
- **Channel-parity and regulated-category flags**
- **AEO / GEO citation scorecard** (`llms.txt` presence, AI-crawler allowlist diff, FAQ/HowTo/Product schema coverage %, answer-first content audit, entity salience notes, baseline citation share by assistant, anti-hallucination policy-page audit)
- **Config-utilization checklist**
- Professional formatting appropriate for retail and e-commerce leadership + engineering
- Correct agentic-commerce terminology (JSON-LD, Schema.org Product, ProductGroup, Offer, AggregateRating, GTIN, MAAI, delegated-purchase token, Shared Payment Token, BNPL agent token, passkey, 3DS2, agent user-agent, Agent Commerce, Web Bot Auth, Visa Trusted Agent Protocol, Mastercard Verifiable Intent, Stripe Link for AI agents, Machine Payments Protocol, Universal Commerce Protocol)
- Saved to `outputs/` if the user confirms

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]
