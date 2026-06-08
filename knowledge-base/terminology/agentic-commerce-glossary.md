# Agentic Commerce Glossary — Retail & E-commerce

*Compiled 2026-06-08 from the agentic-commerce concepts introduced across `sales/brand-agent-authoring`, `sales/agentic-commerce-readiness`, `sales/product-description-writer`, and `operations/agentic-checkout-fraud-shield`. Definitions are written in plain retail terminology for grounding; no external definition is copied verbatim.*

---

## Agents and surfaces

- **Consumer-side (buyer) agent** — an AI agent acting on the shopper's behalf that visits many retailers (ChatGPT shopping, Gemini / AI Mode, Operator, Perplexity Shop, Computer Use). It discovers, compares, and sometimes purchases.
- **Brand (seller) agent** — a retailer-owned agent that speaks for the merchant on the merchant's own surface, grounded in the merchant's catalog and brand voice.
- **Hosting posture** — how a brand agent is deployed: *hosted hyperscaler platform* (managed by AWS / Microsoft / Salesforce), *embedded in a third-party ecosystem* (running inside Gemini Enterprise, Dynamics 365, Shopify), or *fully custom* (merchant-hosted on its own stack).
- **Synthetic shopper / store digital twin** — a simulated AI shopper used to test storefront changes before real customers see them.

## Buyer-vs-seller asymmetry (from `brand-agent-authoring`)

- **Buyer-side-agent asymmetry posture** — the merchant's stance when a strong buyer-side agent negotiates against the brand agent: a defined price floor, offer-equivalence rule, refusal to negotiate against itself, counter-prompt-injection defense, and an attestation preference.
- **Seller model tier** — the model class the merchant runs for its own agent (e.g., a frontier vs. lightweight tier). The **seller-model-tier floor** says no seller-side model should be weaker than the dominant buyer-side model class it meets on a surface; the **upgrade trigger** fires when monitoring detects a buyer-side model-class shift; a named **cost-vs-equity sign-off owner** approves the tier. A hosted-platform deployment inherits a default tier unless the merchant explicitly overrides it.
- **Fleet-level value-extraction monitoring** — aggregation across many agent-vs-agent transactions over a window (default trailing 14 days) tracking realized-vs-floor delta per buyer-side model class, win-vs-walk-away rate, promo-stacking rate, return- and chargeback-rate per class, plus a cohort fairness check that flags when the same shopper class systematically gets different terms depending on which buyer agent it used. Breaches route to the seller-model-tier owner and the legal owner.

## Protocols and payments

- **UCP (Universal Commerce Protocol)** — open agentic-commerce protocol (Google + Shopify + coalition) spanning Cart, Catalog, Loyalty, Post-Purchase, and Ads capabilities.
- **Universal Cart** — a cross-merchant cart that follows the shopper across sites.
- **AP2 (Agent Payments Protocol)** — payment protocol donated to the FIDO Alliance; v0.2 adds a *Human Not Present* mode via **Payment Mandates** for autonomous-agent payment authorization.
- **ACP (Agentic Commerce Protocol)** — OpenAI + Stripe open protocol behind ChatGPT Instant Checkout.
- **Verified / identity-bound agent** — an agent whose identity is cryptographically attested (Web Bot Auth signature, Merchant-Attested Agent ID, Visa Trusted Agent Protocol, Mastercard Verifiable Intent), so its traffic can be allowed and risk-scored rather than blocked by default.
- **Delegated-purchase token** — a scoped payment credential a shopper grants an agent (Shared Payment Token, Stripe Link for AI agents, Adyen Agent Token, Mastercard Agent Pay tokens), binding the credential to a specific agent, merchant, and consent policy.

## Catalog and content data (from `product-description-writer`)

- **Conversational Attributes** — a Google Merchant Center attribute group for conversational surfaces: `question_and_answer` (FAQ pairs an agent can quote), `document_link` (canonical doc deep links), `related_product` (cross-sell / compatible / replacement / bundle partner), `item_group_title` (variant-parent display title), `variant_option` (per-axis customer-facing label and values), `popularity_rank` (per-category tie-breaker).
- **Cross-surface answer reconciliation** — the rule that the same Q&A copy must appear on-page, in the feed's `question_and_answer`, and at the UCP Catalog endpoint, so a brand agent never quotes different answers on different surfaces.
- **AEO / GEO** — Answer Engine Optimization / Generative Engine Optimization: structuring content and citing canonical URLs so AI answer surfaces reference the merchant's own sources.

## Governance

- **Agent-access policy** — a published, defensible stance on which third-party agents a retailer allows, verifies, or blocks, plus how agent-generated ad impressions are filtered before billing. Now legally salient following 2026 disputes over agents that disguised themselves as ordinary browser sessions.
- **Voice owner** — the single accountable party for reconciling brand voice across every agentic surface so all agents inherit the same persona.
