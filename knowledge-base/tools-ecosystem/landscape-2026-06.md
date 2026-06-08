# Retail & E-commerce AI Tools Ecosystem Snapshot — June 2026

*Compiled from the 2026-06-08 landscape monitor run, consolidating ecosystem notes deferred to "next cycle" across the 2026-04-24 → 2026-06-01 monitor logs. Reference material for skills in this repo when describing the tooling landscape — not a vendor endorsement. Concept-level only; no vendor copy is quoted or paraphrased verbatim.*

---

## Why this file exists

Every monitor cycle since late April 2026 closed with a "knowledge-base updates next cycle" list that was never written to disk — `tools-ecosystem/` held only a `.gitkeep`. This snapshot clears that backlog in one pass so the skills have a stable ecosystem reference to ground against, and so future cycles append to a living file rather than re-deferring. It maps the platforms, protocols, and vendor categories the retail skills already name, organized by the function each serves. It is descriptive, not prescriptive: a skill should still grade a merchant's actual stack, not assume any named platform is in use.

---

## 1. Retailer-hosted brand-agent platforms

The dominant 2026 pattern: a retailer deploys a conversational shopping assistant *on its own surface* (site, app, voice), grounded in its own catalog, inventory, and brand voice. Distinct from the consumer-side agents in section 3 that shop *at* retailers. Referenced by `sales/brand-agent-authoring` and `sales/agentic-commerce-readiness`.

- **AWS Agentic Shopping Assistant on Amazon Bedrock AgentCore** — hyperscaler-packaged managed service launched May 27, 2026, productizing the technology behind Amazon's own shopping-assistant surface for outside retailers. Marketed as deployable "in as little as 60 days." Named reference deployment: Tapestry's Kate Spade AI Gift Concierge (built directly on Bedrock AgentCore in April 2026, powered by Anthropic Claude Haiku 4.5; addresses the gift-purchase-stress shopper segment by converting uncertain intent into curated recommendations through natural dialogue). Bedrock AgentCore supplies observability, authentication, and evaluation tooling beneath the assistant layer.
- **Microsoft Copilot Studio (managed) / Microsoft Brand Agents on Shopify** — platform-managed brand-agent hosting; Brand Agents surface a merchant's presence inside Copilot conversations.
- **Salesforce Agentforce (managed)** — Spring '26 release added Commerce Skills for Order Management (conversational self-service order changes, exchanges, status), Commerce Skills for Guided Shopping (NLP recommendations + add-to-cart + conversational checkout), one-click Agentforce setup in Business Manager, Contextual Search with Semantic Understanding, and Agentforce Actions for Point-of-Sale (pilot). Salesforce-published figure: AI assistants growing retail traffic volumes by triple digits.
- **Google Business Agent** — Google's brand-chat-on-Search surface positioned as a virtual sales associate; named early retailers include Lowe's, Michael's, Poshmark, and Reebok. (First logged 2026-04-25/27; carried here for the consolidated map.)
- **Embedded-in-ecosystem variants** — Cognizant Agentic Retail CX on Gemini Enterprise; Argano Retail Clienteling Agent on Microsoft Dynamics 365; Amicis Store Commerce Agent (voice-first).
- **Hosting-posture taxonomy** (used by `brand-agent-authoring`): *hosted hyperscaler platform* (AWS Agentic Shopping Assistant, Copilot Studio managed, Agentforce managed) vs. *embedded in third-party ecosystem* (Cognizant on Gemini, Argano on Dynamics, Brand Agent on Shopify) vs. *fully custom* (merchant-hosted Custom GPT / Claude Project / Gemini Gem / first-party widget). The posture gates the seller-side model-tier decision — a hyperscaler-managed deployment inherits a default model tier unless the merchant explicitly overrides.

## 2. Agentic-commerce protocols and payment rails

The interoperability layer that lets agents discover, compare, and purchase. Referenced by `sales/agentic-commerce-readiness` and `operations/agentic-checkout-fraud-shield`.

- **Universal Commerce Protocol (UCP)** — open protocol co-developed by Google and Shopify with 20+ endorsing retailers and payment networks. Google I/O 2026 (May 19–20) expanded UCP capabilities: Cart, Catalog (real-time variant inventory/pricing), Loyalty (account linking), Post-Purchase (order tracking and returns), and an Ads surface. Paired with **Universal Cart**, a cross-merchant cart following the shopper across the web; the May 20 named-launch-retailer set included Nike, Sephora, Target, Ulta Beauty, Walmart, Wayfair, plus Shopify merchants Fenty and Steve Madden. Geographic expansion to Canada and Australia announced for the coming months, UK to follow.
- **Agent Payments Protocol (AP2)** — donated by Google to the FIDO Alliance on April 28, 2026. AP2 v0.2 adds a *Human Not Present* mode for autonomous-agent payments via Payment Mandates. Named launch merchants included Wayfair, Chewy, Quince.
- **Agentic Commerce Protocol (ACP)** — OpenAI + Stripe, open-sourced; powers ChatGPT Instant Checkout via Stripe.
- **Payment-network agentic programs** — Visa Trusted Agent Protocol (on Web Bot Auth) and Visa Agentic Ready program (global expansion to Asia-Pacific and Latin America announced late April 2026); Mastercard Agent Pay with Verifiable Intent and Agentic Tokens (binding a tokenized credential to a specific agent, merchant scope, and consent policy); American Express Agentic Commerce Experiences (ACE) Developer Kit and Agent Purchase Protection; Stripe Link for AI agents; Machine Payments Protocol (MPP) for agent-paid microtransactions/recurring; BNPL-on-Shared-Payment-Token (Affirm, Klarna).
- **Merchant-attested agent identity** — Merchant-Attested Agent IDs (MAAI), Web Bot Auth signature verification, delegated-purchase tokens. These let agent traffic be *identity-bound rather than blocked by default*.

## 3. Consumer-side shopping agents and agent-traffic governance

Agents that act for the shopper and visit many retailers: OpenAI Operator / ChatGPT shopping, Anthropic Computer Use, Google Gemini / AI Mode / Gemini Spark (consumer personal-agent surface for AI Ultra subscribers), Perplexity Shop, Pinterest AI, and long-tail browser agents.

Governance is now a live legal question, not just a technical one. In a March 2026 dispute, a court ordered Perplexity to stop using its browser agent to purchase on Amazon's marketplace after Amazon argued the agent disguised itself as an ordinary browser session and evaded detection. Practical retailer implications captured in the skills: (a) a published, defensible agent-access policy distinguishing *verified* agents (identity-bound via Web Bot Auth / MAAI / Trusted Agent Protocol) from disguised scrapers; (b) ad-impression filtering so agent-generated traffic is detected and excluded before advertisers are billed; (c) the strategic block-vs-allow-vs-verify decision per agent. `agentic-commerce-readiness` covers the structured-data and robots-directive side; `agentic-checkout-fraud-shield` covers the identity-bound-checkout side. Accenture's oft-cited estimate frames the stakes: >30% of online commerce could run through AI agents by 2030 (~$3.1T).

## 4. Catalog and product-data tooling for conversational surfaces

- **Google Merchant Center Conversational Attributes** — optional attribute group (introduced around Google I/O 2026 / Google Marketing Live, May 19–20) conveying product information for conversational surfaces (AI Mode, Gemini, Universal Cart). Six named attributes: `question_and_answer`, `document_link`, `related_product`, `item_group_title`, `variant_option`, `popularity_rank`. Implemented via supplemental data source or Merchant API; does not change product-approval status. Authored by `sales/product-description-writer` (v2.2), which promotes on-page Q&A into the feed and reconciles it against the UCP Catalog endpoint.
- **Shopify Catalog (MCP / REST) + Checkout Kit** — Winter '26 rollout to all developers, enabling full agentic flows (search → selection → checkout) inside the agent experience. Variant ceiling raised to 2,048 per product.

## 5. Storefront simulation and merchant-side AI assistants

- **Shopify SimGym (AI Research Preview)** — synthetic-shopper / store-digital-twin simulator that previews storefront changes against AI shoppers before real customers see them ("flight simulator for your online store"). First named vendor in the synthetic-shopper-testing category; pipeline-tracked pending a second vendor.
- **Shopify Sidekick (proactive evolution)** — merchant-side assistant that anticipates needs, makes theme/code changes, and executes multi-step tasks from conversation.
- **Shoplazza Athena** — native-platform agentic merchant-admin assistant (back-office product / order / discount / logistics / data-analysis via natural language). Still the single named vendor for the distinct merchant-admin pattern; second-vendor gate not yet met.

## 6. In-store associate and clienteling tooling

Real-time inventory visibility, customer context, and next-best-action delivered to associates' handhelds; endless-aisle lookup, anywhere-checkout, and behavioral/loyalty history for clienteling. Vendors/examples: Salesfloor; Argano Retail Clienteling Agent on Dynamics 365; Salesforce Agentforce guided-shopping/clienteling; The Home Depot's store-localized conversational project-planning agent on Google Cloud (aisle-level navigation and product knowledge). Covered by `sales/clienteling-program-design` and `operations/store-associate-voice-assistant`.

## 7. Returns, reverse logistics, and loss prevention

- **Behavioral return-risk scoring** — UPS Happy Returns *Return Vision* (Jan 2026, piloted with Everlane): per-return behavioral risk score from timing, frequency, and shopper patterns. Adjacent vendors: Appriss Retail, Signifyd Returns.
- **Non-image return inspection** — ReturnPro × Clarity (Feb 2026): X-ray computer-vision inspection of returned items without opening the box, comparing against the original manufacturer profile to detect counterfeits, missing accessories, and alterations.
- **AI-generated damage-claim image fraud** — an emerging measurable category: shoppers submitting AI-generated images of damaged/wrong items to claim refunds on goods received in good condition. Covered by `operations/return-fraud-image-shield`.
- **Loss-prevention computer vision** — vision AI on store and self-checkout camera feeds flagging unscanned/concealed items and scan failures, with auto-generated incident tickets (video clip + transaction data). Industry framing: a meaningful share of returns are fraudulent, and the broader returns ecosystem is measured in the hundreds of billions annually. Covered by `operations/store-shrinkage-cv-shield`.

## 8. Industry-context data points (for grounding, not action)

- NVIDIA's annual State of AI in Retail & CPG survey reports roughly nine in ten retailers increasing AI budgets in 2026, with emphasis on open-source models, agentic AI, and physical AI.
- Commonly cited operational benchmarks from forecasting/replenishment vendors: ~20–30% inventory-cost reduction, fewer stockouts, and reduced markdown losses where AI demand forecasting is deployed. Treat as vendor-reported context, not guarantees.

---

## Maintenance note

Append future cycles' ecosystem notes to this file (or open a dated sibling, e.g. `landscape-2026-07.md`) rather than re-deferring to "next cycle." When a pipeline candidate graduates to a skill, move its entry's emphasis from "vendor map" to a cross-link to the new skill file.
