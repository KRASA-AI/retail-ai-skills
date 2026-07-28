# Retail & E-commerce Skills — Eval & Improvement Changelog

Maintained by the Skill Evaluator scheduled task. Most recent entry first.

---

## 2026-07-20 (automated eval cycle — FIRST CYCLE)

**Evaluated:** all **24 skills** in `skills/` (excluding the 3 cross-industry `_shared/` skills) against the newly-authored **rubric v1.0** (`evals/rubric.yml`, modeled on the roofing library's rubric and adapted to retail terminology). This is the first eval cycle for retail, so the entire evals scaffold was created this run: `evals/rubric.yml`, `evals/results/2026-07-20/` (24 per-skill `.yml` + `summary.md`), and empty `evals/test-cases/` + `evals/fixtures/`. **Every skill was graded cold** with every worked-example number re-derived.

**Averages:** cold **8.25** (24 skills) → **8.38 after repairing the bottom 3**.

**Headline finding — the dominant defect across the library is a placeholder worked example.** Nine skills ship with an Example Output reading verbatim `[This section will be populated by the eval system...]`. That single missing artifact caps output_quality at 6 and is the largest score suppressor in the repo — bigger than any content gap. The affected nine are the newest agentic skills, whose *content* is among the strongest in the library (deepest 2026 protocol fluency: AP2, UCP, Universal Cart, CE 3.0, FTC July-2026 accuracy-suppression theory). This cycle filled three (the bottom 3); **six remain** and are the highest-leverage backlog.

**Cold grading also caught genuine arithmetic/consistency defects in skills that DO have examples** — the same failure mode found in roofing. The most important sits at **rank 18, not the bottom**: `inventory-reorder-brief` (8.0) shows a Net-90 financing benefit of **$88.77** (a 15-day figure) where its own `cost_of_capital × 60/365` formula gives **$355.07**; the stated net (−$91) and its **sign** are wrong, mis-rejecting a real option (final pick unchanged). Others: `distributed-order-allocation` packet totals don't reconcile (18,535 vs 18,400; 603 vs 553); `promotion-campaign-builder` mislabels "700 incremental units" (700 is total, incremental is 200); `customer-service-reply` version-pins its dependency three ways (v2.1/v2.2/v2.3) while the file is v2.6; `clienteling-program-design` calls the GDPR Article 22 right "EU AI Act Article 22."

**Improvements made (bottom 3, all replaced their originals, none degraded):**

1. **Brand Agent Authoring v1.1 → v1.2: 7.0 → 8.1 (+1.1).** Restructured step 9's ~450-word run-on into seven discrete, independently testable lettered clauses (no content removed), and filled the placeholder with a full worked packet for a home-fragrance brand ("Lumi" for *Lumen & Wick*) demonstrating all 13 output sections, binding `$75` self-serve + `365`-day retention and flagging absent `clienteling.segment_owners`. Added a Fastest-path minimum-viable-input block. clarity 6→8, output_quality 6→9, efficiency 6→7.
2. **Product Description Writer v2.2 → v2.3: 7.3 → 8.1 (+0.8).** Fixed a factual error (Required Input #7 "COSRA for cosmetics" → **MoCRA**, correct elsewhere in the same file) and filled the placeholder with a complete *Volaré Lite* stroller listing — Amazon title 128/200, Shopify 57/70, meta 156/160 (**all char counts re-verified with `wc -m`**), five bullets, long-form, Q&A, all six Merchant Center Conversational Attributes, 16/18 Schema.org block, keyword map, compliance log, internal handoff. output_quality 6→9, industry_fit 7→8.
3. **Virtual Try-On & Fit Confidence v1.0 → v1.1: 7.0 → 8.0 (+1.0).** Replaced symbolic thresholds with a 0–100 four-factor eligibility scoring formula and explicit tier cut lines (Now ≥ 60 and fit-return ≥ 12%; Later 6–12%; Skip < 6%) plus numeric rollback triggers (complaint > 2%/7 days; cohort gap > 10 pts), and filled the placeholder with a full worked "Northbourne" audit (six sub-classes scored in a reconciling table, JSON fit-signal schema, measurement/consent/eligibility plans). Power calc re-derived and corrected to ≈ 3,000/cell. specificity 6→8, output_quality 6→9.

All three edits additive or corrective; nothing removed.

**Final ranking (after improvements):**

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
| 15 | **Product Description Writer** | **8.1** (was 7.3) |
| 15 | **Brand Agent Authoring** | **8.1** (was 7.0) |
| 18 | Inventory Reorder Brief | 8.0 |
| 18 | Agentic Checkout Fraud Shield | 8.0 |
| 18 | Customer Service Reply | 8.0 |
| 18 | **Virtual Try-On & Fit Confidence** | **8.0** (was 7.0) |
| 22 | Return Fraud Image Shield | 7.9 |
| 23 | Agentic Commerce Readiness | 7.5 |
| 24 | Agentic Retail Media Mediation | 7.4 |

**Persistent weaknesses:** (1) six remaining placeholder worked examples — the highest-leverage backlog; (2) no `config.sample.yml` fixture, so the personalization ceiling is untestable; (3) empty `test-cases/`; (4) a genuine arithmetic defect (inventory-reorder financing sign) masked at rank 18 by otherwise-9 dimensions.

**Recommendations for next cycle:** (1) fill the six remaining placeholder examples using this cycle's three as templates; (2) repair the inventory-reorder financing line ($88.77 → $355.07) and re-grade; (3) fix the smaller consistency defects (promotion "incremental"→"total", customer-service-reply version-pin sweep, clienteling Article-22 mislabel, planogram inverted signs, distributed-order packet totals); (4) commit `evals/fixtures/config.sample.yml` and grade personalization against it; (5) seed `test-cases/`.

**Verification:** every weighted `overall` recomputed by an independent script — all 24 recompute correctly; 24 skills map 1:1 to 24 result files; cold 198.1/24 = **8.25**, after 201.0/24 = **8.38**. The three improved examples were re-derived programmatically (product-description char counts verified with `wc -m` and corrected to exact values; virtual-try-on power calc corrected ≈2,900 → ≈3,000/cell). **Not committed** — rubric, results, summary, and this entry are in the working tree, left for the repo's daily-sync job.

---
