# Retail & E-commerce Skills — Eval Summary, 2026-07-20

First automated Skill Evaluator cycle for the retail library. **24 skills** in `skills/` (excluding the 3 cross-industry `_shared/` skills) graded against the newly-authored **rubric v1.0**. Because this is the first cycle, there is no carry-forward: **every skill was graded cold**, with every number in every worked example re-derived rather than trusted.

## Headline

- **Cold average: 8.25** (24 skills). **After repairing the bottom 3: 8.38.**
- The library is strong in the middle and top — 8 skills at 8.7+ — but has a **long, thin tail of newer agentic skills held down by one specific, mechanical defect** rather than by weak content.
- **The dominant defect is a placeholder worked example.** Nine skills ship with an Example Output section reading verbatim `[This section will be populated by the eval system with a reference example...]`. That single missing artifact caps output_quality at 6 and is the largest score suppressor in the library — larger than any content gap. The nine: `in-store-retail-media-inventory`, `agentic-checkout-fraud-shield`, `return-fraud-image-shield`, `store-associate-voice-assistant`, `agentic-retail-media-mediation`, `agentic-commerce-readiness`, `product-description-writer`, `brand-agent-authoring`, `virtual-try-on-fit-confidence`. This cycle filled **three** of them (the bottom 3); **six remain** and are the obvious next target.
- **Cold grading caught real arithmetic/consistency defects in skills that *do* have worked examples** — the same failure mode found in the roofing library. None are cosmetic; several would be pasted straight into a supplier decision or a carrier-facing document.

## The bottom of the distribution is mechanical, not substantive

The five lowest-scoring skills (`agentic-retail-media-mediation` 7.4, `agentic-commerce-readiness` 7.5, and the three improved) are, on content, some of the most current and sophisticated in the repo — they carry the deepest 2026 protocol fluency (AP2, UCP, Universal Cart, CE 3.0, FTC July-2026 accuracy-suppression theory). They score low almost entirely because (a) their worked example is a placeholder and (b) a few carry punishing single-sentence run-on density. This is good news: the fix is filling examples and breaking up sentences, not re-researching the domain.

## Arithmetic & consistency defects found (skills with real examples)

| Skill | Score | Defect |
|---|---:|---|
| **inventory-reorder-brief** | **8.0** | Supplier-terms table: Net-90 financing benefit shown as **$88.77** (a 15-day figure) where the skill's own `cost_of_capital × 60/365` formula gives **$355.07** for the 60-day extension. The stated net (−$91) and its **sign** are wrong; correct net ≈ +$175, which flips reject→accept-worthy. Final pick unchanged (Net 60 +$177.53 still wins), but a user reading the table would mis-reject a real option. Everything else (EOQ 1,528, ROP 1,889, landed $8.454, GM 57.7%) re-derives cleanly. **This is the highest-scoring skill with a genuine arithmetic defect and the top improvement candidate for next cycle.** |
| **distributed-order-allocation** | 8.5 | Approval-packet totals don't reconcile: exception classes sum to **18,535** against a stated **18,400** order lines, and a "553 exceptions" note where the reviewer-action classes sum to **603**. Per-line scoring and the $3.78M net are correct. |
| **promotion-campaign-builder** | 8.7 | "Baseline 500 units → **700 incremental** units" mislabels the 1.40× lift: 700 is *total* lifted units, the true incremental is **200**. Downstream revenue and CM correctly treat 700 as total, so the numbers reconcile — only the word "incremental" contradicts them. |
| **customer-service-reply** | 8.0 | Its cross-referenced dependency is version-pinned **three different ways in one file** ("return-policy-explainer v2.1 / v2.2 / v2.3") while the actual file is **v2.6** — every pin stale. Gesture-cap math asserted "within cap" without showing the cap number. |
| **clienteling-program-design** | 8.4 | Calls the automated-decision-making right "**EU AI Act Article 22**" in step 7 and Notes; Article 22 is **GDPR** (the file's own required-input #6 says so). |
| visual-merchandising-planogram-brief | 8.7 | One space-to-sales row has both delta signs inverted vs the table's own convention; verdict unaffected. |
| dynamic-pricing-strategy | 8.8 | Rounded-units-vs-revenue mismatch in the scenario grid ("73 units | $4,813" where 65.99×73 = $4,817). |

## Improvements made (bottom 3 — all replaced their originals, none degraded)

1. **Brand Agent Authoring v1.1 → v1.2: 7.0 → 8.1 (+1.1).** Step 9 was a single ~450-word run-on cramming sub-items (i)–(vii) — model-tier floors, fleet-monitoring metrics — into one unbroken clause. Restructured into seven discrete, independently testable lettered clauses with **no content removed**. Filled the placeholder with a full worked packet for a home-fragrance brand ("Lumi" for *Lumen & Wick*) demonstrating all 13 output sections: dual-form persona spec, grounding table, FAQ leaf, four-tier refusal matrix, regulated-category guardrails, the buyer-side floor/model-tier/fleet-monitor set, and a config-utilization checklist that binds `$75` self-serve + `365`-day retention and flags absent `clienteling.segment_owners`. Added a Fastest-path minimum-viable-input block. clarity 6→8, output_quality 6→9, efficiency 6→7.

2. **Product Description Writer v2.2 → v2.3: 7.3 → 8.1 (+0.8).** Fixed a factual error — Required Input #7 cited "**COSRA** for cosmetics," which is not a real regime; the correct rule is **MoCRA** (used correctly elsewhere in the same file). Filled the placeholder with a complete *Volaré Lite* travel-stroller listing: Amazon title (128/200 chars), Shopify SEO title (57/70), five CAPS-label bullets, a 168-word long-form, a 156-char meta, alt text, a five-pair conversational Q&A, all six Google Merchant Center Conversational Attributes, a 16/18 Schema.org block with the two missing fields flagged, a keyword map, a compliance log, and an internal handoff. **All character counts re-verified with `wc -m`** and corrected to the exact values. output_quality 6→9, industry_fit 7→8.

3. **Virtual Try-On & Fit Confidence v1.0 → v1.1: 7.0 → 8.0 (+1.0).** Key thresholds were symbolic — rollback "complaint rate > X%" and no numeric cut lines for the Now/Next/Later/Skip tiers, forcing the model to invent the decisive numbers. Added a **0–100 four-factor eligibility scoring formula** (fit-return sensitivity 40 / POM data 25 / image consistency 20 / consent coverage 15) with explicit tier cut lines (Now ≥ 60 **and** fit-return ≥ 12%; Later 6–12%; Skip < 6%) and concrete rollback thresholds (complaint > 2% over 7 days; cohort satisfaction gap > 10 pts). Filled the placeholder with a full worked audit for "Northbourne" — six sub-classes scored in a **reconciling** table, a pattern-selection map, a JSON fit-signal schema, and diversity/consent/measurement/eligibility sections. The measurement-plan power calc (24%→21% at 80% power) was re-derived programmatically and corrected to **≈ 3,000/cell**. specificity 6→8, output_quality 6→9.

All three edits are additive or corrective. No formula, table, named binding, or content section was removed.

## Final ranking (after improvements)

| Rank | Skill | Score |
|-----:|-------|------:|
| 1 | Personalization Strategy | 9.1 |
| 2 | Agentic Assortment Planner | 8.9 |
| 2 | Competitive Price Check | 8.9 |
| 2 | Demand Forecasting Brief | 8.9 |
| 2 | Store Shrinkage CV Shield | 8.9 |
| 6 | Dynamic Pricing Strategy | 8.8 |
| 6 | Return Policy Explainer | 8.8 |
| 8 | Labor Scheduling Agent | 8.7 |
| 8 | Visual Merchandising Planogram Brief | 8.7 |
| 8 | Promotion Campaign Builder | 8.7 |
| 11 | Distributed Order Allocation | 8.5 |
| 12 | Agentic Merchant Admin Assistant | 8.4 |
| 12 | Clienteling Program Design | 8.4 |
| 14 | In-Store Retail Media Inventory | 8.2 |
| 15 | Store Associate Voice Assistant | 8.1 |
| 15 | **Product Description Writer** | **8.1** *(was 7.3)* |
| 15 | **Brand Agent Authoring** | **8.1** *(was 7.0)* |
| 18 | Inventory Reorder Brief | 8.0 |
| 18 | Agentic Checkout Fraud Shield | 8.0 |
| 18 | Customer Service Reply | 8.0 |
| 18 | **Virtual Try-On & Fit Confidence** | **8.0** *(was 7.0)* |
| 22 | Return Fraud Image Shield | 7.9 |
| 23 | Agentic Commerce Readiness | 7.5 |
| 24 | Agentic Retail Media Mediation | 7.4 |

## Score distribution (after improvements)

- **9.1:** 1 · **8.9:** 4 · **8.8:** 2 · **8.7:** 3 · **8.5:** 1 · **8.4:** 2 · **8.2:** 1 · **8.1:** 3 · **8.0:** 4 · **7.9:** 1 · **7.5:** 1 · **7.4:** 1

## Persistent weaknesses

- **Six placeholder worked examples remain** (`in-store-retail-media-inventory`, `agentic-checkout-fraud-shield`, `return-fraud-image-shield`, `store-associate-voice-assistant`, `agentic-retail-media-mediation`, `agentic-commerce-readiness`). Each is a ~+1.0 to +1.5 point win from a single filled example, exactly as demonstrated on the three repaired this cycle. This is the single highest-leverage backlog in the library.
- **No config fixture.** Personalization was graded on how well each skill *binds* named config fields and handles their absence — most bind fields well (hence 8–9), but the ceiling can't be tested until an `evals/fixtures/config.sample.yml` exists. Recommended as the roofing library did it.
- **`test-cases/` is empty** (created this cycle with a `.gitkeep`).
- **A genuine arithmetic defect sits at rank 18, not at the bottom.** `inventory-reorder-brief`'s financing sign error scores 8.0 because its clarity/specificity/industry-fit are all 9 — the single wrong number is masked by an otherwise excellent skill. Cold grading is the only thing that surfaces this class of defect.

## Recommendations for next cycle

1. **Fill the six remaining placeholder examples.** Highest-leverage work in the repo; use the three filled this cycle as the template (worked, reconciling, config-bound, with a completeness/consistency checklist).
2. **Repair `inventory-reorder-brief`'s financing line** ($88.77 → $355.07 on the 60-day basis) and re-grade — a rank-18 skill that should be ~8.5+.
3. **Fix the smaller consistency defects:** the promotion "incremental"→"total" label, the customer-service-reply version-pin sweep (v2.1/v2.2/v2.3 → v2.6), the clienteling "EU AI Act Article 22"→"GDPR Article 22" mislabel, the planogram inverted-sign row, and the distributed-order packet totals.
4. **Commit `evals/fixtures/config.sample.yml`** binding the fields the library references (company/banner, margins, platforms, voice, escalation_thresholds, regulated_categories, jurisdictions, target_assistants) with a few fields deliberately omitted to keep graceful-degradation testable. Grade personalization against it from the next cycle.
5. **Seed `test-cases/`** with per-skill input fixtures.

## Verification pass

Every weighted `overall` was recomputed by an independent script from the recorded dimension scores: all 24 recompute correctly, the 24 skills map 1:1 to 24 result files, the cold average is 198.1/24 = **8.25** and the post-improvement average is 201.0/24 = **8.38**. The three improved worked examples were re-derived programmatically — the product-description character counts (`128/57/156`) were verified with `wc -m` and the stated numbers corrected to match, and the virtual-try-on power calc was corrected from a first-draft ≈2,900 to the true ≈3,000/cell. All three skill diffs are additive or corrective with no content loss.

**Not committed.** The rubric, all 24 result files, this summary, and the changelog entry are written to the working tree but left uncommitted, consistent with this task's read-mostly remit — the repo's daily-sync job commits tracked changes.
