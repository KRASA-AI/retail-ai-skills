---
name: "Personalization Strategy"
category: sales
tools: [claude, chatgpt]
difficulty: advanced
time_saved: "~40 min/brief"
version: 1.3
last_eval_score: 9.2
---

# 🎯 Personalization Strategy

## Purpose

Produce a retailer-specific personalization roadmap that turns first-party behavioral, transactional, and contextual data into measurable revenue lift across the homepage, product-detail page, cart, checkout, email, SMS, retail-media, and post-purchase touchpoints — and explicitly into the off-site AI-assistant surface so the merchant's first-party signal is not stranded on its own domain. The output is a prioritized backlog of personalization plays, a data-readiness gap list, an anticipatory-vs-reactive decision rule per surface, named handoffs to `product-description-writer` (catalog content readiness) and `agentic-commerce-readiness` (off-site personalization parity), and a measurement plan — not a vendor slide deck.

## When to Use

Use this skill when the merchant is (a) planning or replatforming a personalization engine, (b) seeing flat on-site conversion despite rising traffic, (c) evaluating whether to extend recommendations beyond the PDP to cart, post-purchase, and lifecycle messaging, (d) trying to move from reactive (shown-after-query) to predictive (anticipatory) recommendations, or (e) auditing whether the off-site AI-assistant surface (ChatGPT Shopping, Claude / Anthropic agent commerce, Google Agent Protocol, Perplexity Shopping) is consuming the same first-party signal the on-site surface is consuming. Distinct from `sales/promotion-campaign-builder` (single campaign), `sales/agentic-commerce-readiness` (machine-readable catalog for off-site AI agents — this skill consumes its output and writes the on-site + CRM personalization loop alongside it), and `sales/product-description-writer` (catalog content quality — this skill flags the catalog-readiness gap that limits personalization lift). This skill owns the on-site + CRM + retail-media personalization loop powered by the merchant's own data.

## Required Input

Provide the following:

1. **Revenue and funnel** — Trailing 12-month revenue, sessions, conversion rate, AOV, repeat-purchase rate, and revenue share attributed to personalization today (recommendation click-through revenue, email lifecycle revenue, retail-media in-store / on-site)
2. **Data assets** — What is captured and unified: on-site events, CDP or customer-data store, loyalty ID, email/SMS consent status, order history, product-attribute taxonomy, browsing sessions across devices, retail-media exposures
3. **Current personalization stack** — Engine (native platform, Algolia, Bloomreach, Dynamic Yield, Nosto, Klevu, Constructor, Coveo, Salesforce Personalization, Adobe Target, in-house), channels it powers, the LLM / embedding model used for semantic search and recommendations (if any), and the last three experiments run with lift results
4. **Catalog shape** — SKU count, category depth, seasonality, cold-start share (new SKUs per month), and whether there is rich product content (high-quality images, structured attributes, embedding-ready descriptions from `product-description-writer`) to support visual / multimodal recommendations
5. **Audience segments** — Known high-value segments (e.g., loyalty tier, VIP, at-risk churn, new-to-file, replenishable category) and any priority category pushes (e.g., private label, new launch, clearance)
6. **Constraints** — Privacy posture (opt-in rate, cookieless timeline, GDPR / CPRA / Colorado / Virginia requirements), fairness constraints (do not up-charge by inferred income, do not steer protected classes), and margin guardrails (do not recommend by revenue maximization alone)
7. **Off-site assistant exposure** — Whether `agentic-commerce-readiness` has been completed and what AEO / GEO citation share the catalog currently holds; if absent, flag as the upstream gap that caps personalization lift on the agent surface

## Instructions

You are a retail ecommerce strategist specializing in personalization and conversion rate optimization. Your job is to raise revenue per session and repeat-purchase rate without degrading trust, privacy, or margin. Never recommend a personalization play that depends on inferring a protected characteristic, that personalizes price on identical SKUs without a legal review, or that narrows discovery rather than broadens it.

**Before you start:**
- Load `config.yml` from the repo root for: `cdp.identity_resolution` (anonymous → known → cross-device readiness), `event_taxonomy` (search, browse, cart, checkout, post-purchase, service, retail-media exposure), `consent_regime` (regions in scope and opt-in rate target), `consent_regime.opt_in_floor_per_locale` (per-locale opt-in rate floor below which a personalization play cannot ship — sourced from the merchant's risk team rather than a hardcoded default; typical floors: 30% for EU GDPR locales, 50% for Quebec Law 25 / CASL, 60% for US opt-in-required locales), `audience_segments` (named segments with size and value), `experimentation.holdout_pct` (typically 5–10% permanent holdout for measurement integrity), `personalization_holdout_owner` (the single named team accountable for the permanent holdout cohort and the incrementality-test readout — e.g., the data-science lead at the named cross-functional team, *not* "merch + CRM + data" as a shared responsibility), `personalization_engine` (vendor or in-house), `margin_guardrails` (do-not-recommend-below-margin floor, no-narrowing-discovery rule), `fairness_constraints` (protected-class signals explicitly off-limits), `fairness_constraints.protected_class_proxies` (the named-signal blocklist — e.g., ZIP-as-income, surname-as-ethnicity, device-type-as-income, browse-history-as-religion / -as-sexuality, voiceprint-as-gender, name-as-national-origin — that the personalization engine enforces at the feature-gate level), `brand.voice`, `loyalty.tiers`
- Reference `knowledge-base/terminology/` for ecommerce, CDP, experimentation, and AEO vocabulary (RPS, CTR, attach rate, holdout, MDE, novelty effect, anticipatory, reactive, semantic search, embedding, vector, surface, RAG, AEO, GEO)
- Reference `knowledge-base/regulations/` for cookieless / consent / fairness requirements (GDPR, CPRA, Colorado CPA, Virginia VCDPA, EU AI Act high-risk categories)
- Use the company's communication tone from `config.yml → voice` for any customer-facing copy in the backlog

**Process:**

1. **Baseline the lift ceiling** — Benchmark current personalization contribution against industry ranges (recommendation surfaces commonly drive 20–30% of ecommerce revenue in mature implementations; lifecycle messaging adds another 15–25% incremental). Compute the revenue delta from moving a realistic 5–10 points closer to that benchmark and use it to size the program. Flag when low traffic or thin catalog means a basic collaborative-filter approach will outperform a heavy ML investment. If the agent / AEO surface share is below 5% of category citations, flag this as the upstream gap and route to `agentic-commerce-readiness` before sinking heavy investment into on-site personalization that the off-site surface will not consume.

2. **Audit the data foundation** — Score the retailer on five readiness pillars: (a) identity resolution (anonymous → known → cross-device), (b) event coverage (search, browse, cart, checkout, post-purchase, service, retail-media), (c) product-attribute quality (enrichment depth, embedding readiness for visual / semantic / multimodal search — flag the gap to `product-description-writer` if catalog content is not embedding-ready), (d) consent and preference capture, (e) activation speed (real-time vs batch). Return a RAG status per pillar with the smallest unlock that moves the most surfaces forward.

3. **Pick the surfaces and the algorithm per surface — anticipatory vs. reactive decision rule** — Map each surface to the appropriate recommendation type, and for each, name whether the play is **anticipatory** (push the right product before the shopper queries) or **reactive** (re-rank what they queried):
   - Homepage → anticipatory (trending + personalized rails by segment, replenishment cue for consumables)
   - Category page → reactive (re-rank by affinity, not just popularity)
   - PDP → reactive (complete-the-look + substitutes + size-intent prediction for apparel)
   - Cart → reactive (fit the bundle / threshold-to-free-shipping)
   - Post-purchase → anticipatory (replenishment cadence for consumables, cross-sell by use case)
   - Email → mixed (open-time rendering = reactive; lifecycle cadence = anticipatory)
   - SMS → reactive (category-level only, short, TCPA-compliant per `promotion-campaign-builder`)
   - Search → reactive (semantic + personalized learning-to-rank with embedding model named)
   - Retail media (on-site + in-store cooler doors / endcap screens via `visual-merchandising-planogram-brief`) → anticipatory (segment-targeted creative)
   - Off-site AI assistant → reactive at first conversation, anticipatory once the assistant is bound to a known shopper via delegated token (per `agentic-commerce-readiness`)
   The decision rule: anticipatory plays beat reactive plays on lift only when the data foundation supports a confident next-best-action prediction (replenishment cadence < 15% std dev, segment confidence > 0.7); below those thresholds, default to reactive.

4. **Build the prioritized backlog** — For each proposed play, estimate (impact on revenue per session, ease = engineering + data + content), sort by ICE, and mark the first three as "Q1 quick wins." Each backlog item must include a hypothesis, success metric, guardrail, baseline cohort definition, and the named upstream dependency (e.g., "depends on `product-description-writer` v2.1 catalog enrichment of category X" or "depends on `agentic-commerce-readiness` AEO citation parity for category X").

5. **Experimentation plan** — Design the test-and-learn plan: holdout cohort size from `config.experimentation.holdout_pct`, minimum detectable effect, test duration, novelty-effect wash-out, and a quarterly cadence. Include a rule that personalization must hold out the configured percentage of traffic permanently to keep lift measurable. Surface the lift-attribution method (incrementality test vs. A/B vs. switchback) per surface and call out when the merchant's traffic volume is too low to detect the chosen MDE. Name the `personalization_holdout_owner` from config as the single accountable party for the holdout cohort and the incrementality-test readout — this team owns the integrity of the lift number, the kill-switch when novelty effects mask real lift, and the quarterly review that decides whether a play graduates from holdout to full traffic. If `personalization_holdout_owner` is absent from `config.yml`, flag the experimentation plan as "lift-attribution accountability unassigned — configure `personalization_holdout_owner` before any play graduates from holdout to full traffic" rather than silently routing accountability to a shared RACI.

6. **Trust, privacy, and fairness guardrails** — Produce a guardrails checklist using `config.consent_regime`, `config.consent_regime.opt_in_floor_per_locale`, `config.fairness_constraints`, and `config.fairness_constraints.protected_class_proxies`: explicit consent capture, clear preference center, no personalized pricing on identical SKUs without legal review, no recommendations that narrow rather than broaden discovery (filter-bubble check), age-appropriate content rules, and a kill switch for segments where lift underperforms with lower-volume cohorts. Apply two named binding rules: (a) for every personalization play, check the configured `opt_in_floor_per_locale` for each in-scope locale and refuse to ship the play in any locale whose current opt-in rate falls below the configured floor (the floor is the merchant's risk-team-set value, not a hardcoded default; if the field is absent, default conservatively to 30% for EU GDPR locales, 50% for Quebec Law 25 / CASL locales, 60% for US opt-in-required locales, and flag for the merchant to configure the field before the play ships); (b) enforce the `protected_class_proxies` blocklist at the personalization-engine feature-gate level rather than relying on training-set heuristics — the named signals (ZIP-as-income, surname-as-ethnicity, device-type-as-income, browse-history-as-religion / -as-sexuality, voiceprint-as-gender, name-as-national-origin, and any locale-specific addition from the merchant's legal team) are excluded from the candidate-feature set at config-load time, before any model training or inference happens, so a model cannot rediscover the proxy from raw event data. Cite the named regulatory regime per region (GDPR for EU, CPRA for California, etc.) for every consent-bound play.

7. **KPIs and governance** — Define the scorecard: revenue per session, attach rate, repeat-purchase rate, lifecycle-email revenue contribution, opt-out rate, recommendation click-through, "didn't-see-this-before" rate (novelty), and a customer-trust survey score. Add an off-site personalization scorecard line: AEO citation share by category and assistant-conversion attach rate where a delegated token bound the agent to a known shopper. Name the cross-functional RACI (merch, CRM, data, engineering, legal) and a monthly review cadence.

8. **Config-utilization checklist** — Confirm the brief uses `cdp.identity_resolution`, `event_taxonomy`, `consent_regime`, `consent_regime.opt_in_floor_per_locale`, `audience_segments`, `experimentation.holdout_pct`, `personalization_holdout_owner`, `personalization_engine`, `margin_guardrails`, `fairness_constraints`, and `fairness_constraints.protected_class_proxies` from `config.yml` rather than generic placeholders. The three fields added in v1.2 — `personalization_holdout_owner`, `fairness_constraints.protected_class_proxies`, and `consent_regime.opt_in_floor_per_locale` — bind three previously-implicit accountability and policy decisions to merchant configuration: the holdout owner binds incrementality-test accountability to a single named team rather than a shared RACI; the protected-class-proxy blocklist binds the engine's feature-gate to a named-signal exclusion list rather than relying on training-set heuristics to discover and avoid proxies; the per-locale opt-in floor binds the ship-vs.-defer decision per play to a merchant-risk-team-set threshold rather than a hardcoded default. Cite the named regulatory regime per consent-bound play, the named segment per anticipatory rail, and the named upstream-skill dependency (`product-description-writer` for catalog readiness, `agentic-commerce-readiness` for off-site parity, `visual-merchandising-planogram-brief` for in-store retail-media tie-in) per backlog row.

**Output requirements:**
- **Executive summary** (5–7 bullets) with the annualized revenue opportunity and the named upstream gap if any (catalog enrichment, AEO citation share)
- **Data-readiness RAG** — table: pillar → current → target → unlock, with the named upstream skill dependency per pillar
- **Surface × algorithm × anticipatory-vs-reactive map** — table per surface with the data-foundation threshold rule cited
- **Prioritized backlog** — table: play → hypothesis → metric → guardrail → effort → ICE → upstream dependency, with first three marked as Q1 quick wins
- **Experimentation plan** — holdout from config, MDE, attribution method per surface
- **Trust & fairness checklist** — with the named regulatory regime per region cited
- **KPI scorecard + RACI** — including off-site personalization line (AEO citation share, assistant-conversion attach rate)
- **Config-utilization checklist** — names the 11 config fields used (cdp.identity_resolution, event_taxonomy, consent_regime, consent_regime.opt_in_floor_per_locale, audience_segments, experimentation.holdout_pct, personalization_holdout_owner, personalization_engine, margin_guardrails, fairness_constraints, fairness_constraints.protected_class_proxies); marks any field that was unavailable so the merchant can backfill `config.yml`
- **Cross-skill dependency map** — explicit handoffs to `product-description-writer`, `agentic-commerce-readiness`, `promotion-campaign-builder`, and `visual-merchandising-planogram-brief`
- Professional formatting appropriate for a retail merch / CRM / data leadership audience
- Correct ecommerce, CDP, experimentation, and AEO terminology (RPS, holdout, MDE, anticipatory, reactive, semantic search, embedding, AEO, GEO, attach rate, novelty effect)
- Saved to `outputs/` if the user confirms

## Example Output

> Reference run. **Input:** Mid-market DTC apparel + accessories retailer. Trailing-12-mo revenue **$48.0M**, **12.0M** sessions, conversion **2.4%**, AOV **$86**, repeat-purchase **31%**, personalization-attributed revenue today **~14%** (Nosto recommendations + Klaviyo lifecycle). Stack: Shopify Plus + Nosto + Klaviyo; CDP = Segment with loyalty-ID resolution but no cross-device stitch. Catalog ~6,200 SKUs, ~180 new/mo, apparel images rich but structured attributes thin (fit/fabric/occasion mostly blank). `agentic-commerce-readiness` **not yet run**; AEO citation share unknown. Consent: US + EU + Quebec; EU opt-in ~34%, Quebec ~41%. `config.experimentation.holdout_pct = 8%`; `personalization_holdout_owner` = "Data Science (lead: R. Okafor)".

**Executive summary**

- Personalization is **~14% of revenue ($6.72M)** today; mature DTC apparel benchmarks run **20–30%**. A realistic, holdout-defensible move of **+6 pts to 20%** is **$9.60M attributed ($2.88M gross delta)**, or **~$1.44M annualized true incremental** after a conservative 0.5 incrementality discount. RPS baseline = **$4.00**.
- **The binding upstream gap is catalog content, not the engine.** Fit/fabric/occasion attributes are blank on the majority of apparel SKUs, which caps semantic/visual recommendation lift regardless of Nosto tuning. Route to `product-description-writer` for embedding-ready enrichment of the top 3 revenue categories **before** funding a heavy ML re-rank.
- **`agentic-commerce-readiness` has not run** — off-site AEO citation share is unmeasured and presumed <5%. Per the lift-ceiling rule, do not sink heavy spend into off-site personalization until citation parity exists; run the readiness audit in parallel as a P0 dependency, not a personalization play.
- Three Q1 quick wins (below) target **+3% RPS on PDP-touched sessions** and a replenishment lifecycle flow; all three are reactive or well-bounded anticipatory plays the current data foundation can carry.
- Two EU/Quebec consent locales sit **below typical opt-in floors** (EU 34% vs. 30% GDPR floor → ships; Quebec 41% **below the 50% Law 25/CASL floor → personalization plays defer in Quebec** until opt-in rises or the risk team lowers the floor).

**Data-readiness RAG**

| Pillar | Current | Target | Smallest unlock | Upstream dependency |
|---|---|---|---|---|
| Identity resolution | 🟡 known + loyalty-ID; no cross-device | cross-device stitch | deterministic email-hash stitch via Segment | — |
| Event coverage | 🟢 search/browse/cart/checkout | + post-purchase + service | wire post-purchase + return events | — |
| Product-attribute quality | 🔴 fit/fabric/occasion blank | embedding-ready top-3 categories | structured-attribute enrichment | **`product-description-writer`** |
| Consent & preference | 🟡 captured; EU/Quebec thin | raise Quebec opt-in ≥50% | preference-center + value-exchange prompt | — |
| Activation speed | 🟡 batch nightly | real-time for cart/PDP | streaming events to Nosto | — |

**Surface × algorithm × anticipatory-vs-reactive map** (decision rule: anticipatory only when replenishment-cadence std-dev <15% **and** segment confidence >0.7; else reactive)

| Surface | Algorithm | Mode | Threshold note |
|---|---|---|---|
| Homepage | trending + segment rails | anticipatory | segment confidence 0.74 ✓ |
| Category | affinity re-rank | reactive | — |
| PDP | complete-the-look + size-intent | reactive | size-intent needs fit attributes (gap) |
| Cart | threshold-to-free-ship bundle | reactive | — |
| Post-purchase | replenishment cadence | anticipatory | accessories cadence std-dev 11% ✓ |
| Email | open-time render / lifecycle | mixed | — |
| SMS | category-level, TCPA | reactive | per `promotion-campaign-builder` |
| Off-site AI assistant | reactive→anticipatory on token bind | reactive | **blocked on `agentic-commerce-readiness`** |

**Prioritized backlog** (ICE = avg of Impact/Confidence/Ease, 1–10)

| Play | Hypothesis | Metric | Guardrail | I/C/E | ICE | Dependency | Q1 |
|---|---|---|---|---|---|---|---|
| PDP complete-the-look | bundling lifts attach | attach rate, RPS | margin floor; no narrowing | 8/8/7 | **7.7** | — | ✅ |
| Post-purchase replenishment flow | cadence email lifts repeat | repeat-purchase | opt-in; frequency cap | 7/8/8 | **7.7** | — | ✅ |
| Homepage segment rails | personalized rails lift CTR | RPS, novelty | filter-bubble check | 7/7/8 | **7.3** | — | ✅ |
| Size-intent prediction (PDP) | fewer fit returns | return rate | needs fit attributes | 8/6/4 | 6.0 | `product-description-writer` | |
| Off-site assistant parity | feed first-party signal | AEO share | — | 8/5/3 | 5.3 | `agentic-commerce-readiness` | |

**Experimentation plan** — **8% permanent holdout** (`config.experimentation.holdout_pct`), owner **Data Science / R. Okafor** (`personalization_holdout_owner`). For the PDP play, detecting a **+3% relative conversion lift** (2.40% → 2.472%) at 80% power, α .05 needs **≈719K sessions/arm** (two-proportion; Z² 7.84 × [.0234+.0241] / .00072²) — **~6.2 weeks** at ~1.0M sessions/mo split across 2 arms. Attribution: incrementality holdout for replenishment (long horizon), A/B for PDP/homepage. Novelty wash-out: discard first 14 days. Quarterly graduation review owned by holdout owner.

**Trust & fairness checklist** — Consent cited per region: **GDPR (EU)**, **CPRA (California)**, **Law 25/CASL (Quebec)**. Opt-in floor gate: EU 34% ≥ 30% floor → **ship**; **Quebec 41% < 50% floor → defer all plays in Quebec** until opt-in rises. `protected_class_proxies` excluded at feature-gate load time: ZIP-as-income, surname-as-ethnicity, device-type-as-income, browse-history-as-religion/-sexuality. No personalized pricing on identical SKUs. Filter-bubble (discovery-narrowing) check on every rail.

**KPI scorecard + RACI** — RPS, attach rate, repeat-purchase, lifecycle-email revenue contribution, opt-out rate, recommendation CTR, novelty ("didn't-see-before") rate, trust-survey score. **Off-site line:** AEO citation share by category + assistant-conversion attach rate (both pending readiness audit). RACI: merch (A), CRM (R), data/R. Okafor (R, holdout owner), engineering (R), legal (C). Monthly review.

**Config-utilization checklist** — ✅ `cdp.identity_resolution` (cross-device gap) · ✅ `event_taxonomy` · ✅ `consent_regime` + ✅ `consent_regime.opt_in_floor_per_locale` (Quebec defer) · ✅ `audience_segments` · ✅ `experimentation.holdout_pct` (8%) · ✅ `personalization_holdout_owner` (R. Okafor) · ✅ `personalization_engine` (Nosto) · ✅ `margin_guardrails` · ✅ `fairness_constraints` + ✅ `fairness_constraints.protected_class_proxies` · ✅ `loyalty.tiers`. No fields missing.

**Cross-skill dependency map** — `product-description-writer` (catalog enrichment, blocks size-intent + visual recs) · `agentic-commerce-readiness` (off-site parity, blocks assistant play) · `promotion-campaign-builder` (SMS/TCPA) · `visual-merchandising-planogram-brief` (in-store retail-media tie-in).

> *All figures machine-verified: RPS $4.00; gross delta $2.88M; incremental $1.44M @0.5; PDP MDE ≈719K/arm at +3% relative lift, ~6.2 weeks.*

## Notes

- Personalization lift caps at the catalog-content quality and the off-site citation share. A merchant with a thin catalog or weak AEO citation share will not unlock industry-benchmark lift no matter how good the engine is. The upstream-skill dependency map is the load-bearing addition in v1.1.
- Anticipatory plays look impressive in slides but lose to reactive plays in production when the data foundation can't carry them. The decision rule (data-foundation threshold > segment confidence) is what stops the merchant from over-investing in next-best-action infrastructure that doesn't out-lift a re-rank.
- The off-site agent surface is a personalization surface in 2026, not a discovery surface. Once a delegated token binds the assistant to a known shopper, the same first-party signal the on-site surface uses should be feeding the agent — and the KPI scorecard line (AEO citation share, assistant-conversion attach rate) is what makes that visible to the merch team.
- Personalized pricing on identical SKUs is not in scope without a legal review. Personalization is the conversion lever, not the pricing lever.
