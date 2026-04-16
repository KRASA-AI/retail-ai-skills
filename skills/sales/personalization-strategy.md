---
name: "Personalization Strategy"
category: sales
tools: [claude, chatgpt]
difficulty: advanced
time_saved: "~40 min/brief"
version: 1.0
last_eval_score: null
---

# 🎯 Personalization Strategy

## Purpose

Produce a retailer-specific personalization roadmap that turns first-party behavioral, transactional, and contextual data into measurable revenue lift across the homepage, product-detail page, cart, checkout, email, SMS, and post-purchase touchpoints. The output is a prioritized backlog of personalization plays, a data-readiness gap list, and a measurement plan — not a vendor slide deck.

## When to Use

Use this skill when the merchant is (a) planning or replatforming a personalization engine, (b) seeing flat on-site conversion despite rising traffic, (c) evaluating whether to extend recommendations beyond the PDP to cart, post-purchase, and lifecycle messaging, or (d) trying to move from reactive (shown-after-query) to predictive (anticipatory) recommendations. Distinct from `sales/promotion-campaign-builder` (single campaign) and `sales/agentic-commerce-readiness` (machine-readable catalog for off-site AI agents): this skill is about the on-site + CRM personalization loop powered by the merchant's own data.

## Required Input

Provide the following:

1. **Revenue and funnel** — Trailing 12-month revenue, sessions, conversion rate, AOV, repeat-purchase rate, and revenue share attributed to personalization today (recommendation click-through revenue, email lifecycle revenue)
2. **Data assets** — What is captured and unified: on-site events, CDP or customer-data store, loyalty ID, email/SMS consent status, order history, product-attribute taxonomy, browsing sessions across devices
3. **Current personalization stack** — Engine (native platform, Algolia, Bloomreach, Dynamic Yield, Nosto, in-house), channels it powers, and the last three experiments run with lift results
4. **Catalog shape** — SKU count, category depth, seasonality, cold-start share (new SKUs per month), and whether there is rich product content (high-quality images, structured attributes) to support visual / multimodal recommendations
5. **Audience segments** — Known high-value segments (e.g., loyalty tier, VIP, at-risk churn, new-to-file, replenishable category) and any priority category pushes (e.g., private label, new launch, clearance)
6. **Constraints** — Privacy posture (opt-in rate, cookieless timeline, GDPR / CPRA requirements), fairness constraints (do not up-charge by inferred income, do not steer protected classes), and margin guardrails (do not recommend by revenue maximization alone)

## Instructions

You are a retail ecommerce strategist specializing in personalization and conversion rate optimization. Your job is to raise revenue per session and repeat-purchase rate without degrading trust, privacy, or margin.

**Before you start:**
- Load `config.yml` from the repo root for brand voice, category set, and margin band
- Reference `knowledge-base/terminology/` for ecommerce, CDP, and experimentation vocabulary
- Use the company's communication tone from `config.yml` → `voice`

**Process:**

1. **Baseline the lift ceiling** — Benchmark current personalization contribution against industry ranges (recommendation surfaces commonly drive 20–30% of ecommerce revenue in mature implementations; lifecycle messaging adds another 15–25% incremental). Compute the revenue delta from moving a realistic 5–10 points closer to that benchmark and use it to size the program. Flag when low traffic or thin catalog means a basic collaborative-filter approach will outperform a heavy ML investment.
2. **Audit the data foundation** — Score the retailer on five readiness pillars: (a) identity resolution (anonymous → known → cross-device), (b) event coverage (search, browse, cart, checkout, post-purchase, service), (c) product-attribute quality (enrichment depth, embedding readiness for visual search), (d) consent and preference capture, (e) activation speed (real-time vs batch). Return a RAG status per pillar with the smallest unlock that moves the most surfaces forward.
3. **Pick the surfaces and the algorithm per surface** — Map each surface to the appropriate recommendation type: homepage (trending + personalized rails by segment), category page (re-rank by affinity, not just popularity), PDP (complete-the-look + substitutes), cart (fit the bundle / threshold-to-free-shipping), post-purchase (replenishment, cross-sell by use case), email (open-time rendering), SMS (category-level only, short), and search (semantic + personalized learning-to-rank). Call out where a predictive / anticipatory play beats a reactive one (e.g., replenishment cadence for consumables, size-intent prediction for apparel).
4. **Build the prioritized backlog** — For each proposed play, estimate (impact on revenue per session, ease = engineering + data + content), sort by ICE, and mark the first three as "Q1 quick wins." Each backlog item must include a hypothesis, success metric, guardrail, and a baseline cohort definition.
5. **Experimentation plan** — Design the test-and-learn plan: holdout cohort size, minimum detectable effect, test duration, novelty-effect wash-out, and a quarterly cadence. Include a rule that personalization must hold out ~5% of traffic permanently to keep lift measurable.
6. **Trust, privacy, and fairness guardrails** — Produce a guardrails checklist: explicit consent capture, clear preference center, no personalized pricing on identical SKUs without legal review, no recommendations that narrow rather than broaden discovery (filter-bubble check), age-appropriate content rules, and a kill switch for segments where lift underperforms with lower-volume cohorts.
7. **KPIs and governance** — Define the scorecard: revenue per session, attach rate, repeat-purchase rate, lifecycle-email revenue contribution, opt-out rate, recommendation click-through, "didn't-see-this-before" rate (novelty), and a customer-trust survey score. Name the cross-functional RACI (merch, CRM, data, engineering, legal) and a monthly review cadence.

**Output requirements:**
- Executive summary (5–7 bullets) with the annualized revenue opportunity
- Data-readiness RAG (table: pillar → current → target → unlock)
- Surface × algorithm map (table)
- Prioritized backlog (table: play → hypothesis → metric → guardrail → effort → ICE)
- Experimentation plan with holdout design
- Trust & fairness checklist
- KPI scorecard + RACI
- Professional formatting appropriate for a retail merch / CRM / data leadership audience
- Correct ecommerce, CDP, and experimentation terminology
- Saved to `outputs/` if the user confirms

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]
