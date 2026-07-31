# Retail & E-commerce Skills — Eval & Improvement Changelog

Maintained by the Skill Evaluator scheduled task. Most recent entry first.

---

## 2026-07-28 (automated eval cycle — SECOND CYCLE)

**Evaluated:** all **24 skills** re-graded cold against rubric v1.0. Results in `evals/results/2026-07-28/` (24 per-skill `.yml` + `summary.md`).

**Averages:** cold **8.37** → **8.49 after repairing the bottom 3.** Up from the 2026-07-20 post-improvement average of 8.38. 21 of 24 skills scored identically to the 2026-07-20 baseline (no file changes, stable re-grading). One skill moved down on closer inspection — `promotion-campaign-builder` 8.7 → 8.5 — not a regression but a newly-caught defect (SMS caption captioned "148 chars" against an actual 110-character string).

**Bottom 3 (same three flagged as the top backlog item last cycle) — all repaired, all replaced their originals, none degraded:**

1. **Agentic Retail Media Mediation v1.1 → v1.2: 7.4 → 8.6 (+1.2).** Filled the placeholder Example Output with a full worked mediation packet for a fictional apparel retailer ("Denning & Co.") across two conversational surfaces (16,000 turns/day): bid floors via `max(reserve, comparable CPC, yield-protection floor)`, a reconciling win/CTR/revenue funnel (3,690 won impressions, $210.06/day gross spend, $94.53/day net at 45% take rate, $5.91 RPM/1,000 turns), and a 23.06%-of-25% sponsored-load check — all independently re-derived and confirmed. Restructured the ~290-word run-on "When to Use" sentence into a lettered list and de-densified Instructions steps 9 and 14, with zero content removed (diff-confirmed). clarity 7→8, output_quality 6→9, efficiency 6→7.
2. **Agentic Commerce Readiness v2.6 → v2.7: 7.5 → 8.4 (+0.9).** Filled the placeholder with a full readiness audit for a fictional outdoor-gear retailer ("Trailforge Supply Co.", 3,180 SKUs): structured-data weighted scoring worked for two SKUs (90.0% and 60.0%), a tier-weighted 0–100 composite (72), and channel-parity/AEO percentages all independently re-derived and confirmed. Corrected a stale frontmatter `last_eval_score` (was 8.2, didn't match the real 7.5). output_quality 6→9, personalization 8→8.5.
3. **Return Fraud Image Shield v1.3 → v1.4: 7.9 → 8.7 (+0.8).** Filled the placeholder with a full worked packet for a fictional outdoor retailer ("Ridgeline Trail Co.", 130,000 claims/yr): net-opportunity, tier-distribution, retro-audit, and composite-weight arithmetic all independently re-derived and confirmed. De-duplicated the 11-field config-utilization checklist (step 10) into concise field + binding-pointer entries without removing any field or mechanism detail. output_quality 6→9, clarity 8→8.5.

All three edits additive or corrective; nothing removed (confirmed by line-level `git diff` review, not just each improving agent's self-report).

**Final ranking (after improvements):**

| Rank | Skill | Score |
|-----:|-------|------:|
| 1 | Personalization Strategy | 9.1 |
| 2 | Agentic Assortment Planner | 8.9 |
| 2 | Competitive Price Check | 8.9 |
| 2 | Demand Forecasting Brief | 8.9 |
| 2 | Store Shrinkage CV Shield | 8.9 |
| 6 | Return Policy Explainer | 8.8 |
| 6 | Dynamic Pricing Strategy | 8.8 |
| 8 | Visual Merchandising Planogram Brief | 8.7 |
| 8 | Labor Scheduling Agent | 8.7 |
| 8 | **Return Fraud Image Shield** | **8.7** *(was 7.9)* |
| 11 | **Agentic Retail Media Mediation** | **8.6** *(was 7.4)* |
| 12 | Distributed Order Allocation | 8.5 |
| 12 | Promotion Campaign Builder | 8.5 *(was 8.7)* |
| 14 | **Agentic Commerce Readiness** | **8.4** *(was 7.5)* |
| 14 | Clienteling Program Design | 8.4 |
| 14 | Agentic Merchant Admin Assistant | 8.4 |
| 17 | In-Store Retail Media Inventory | 8.2 |
| 18 | Product Description Writer | 8.1 |
| 18 | Brand Agent Authoring | 8.1 |
| 18 | Store Associate Voice Assistant | 8.1 |
| 21 | Customer Service Reply | 8.0 |
| 21 | Agentic Checkout Fraud Shield | 8.0 |
| 21 | Inventory Reorder Brief | 8.0 |
| 21 | Virtual Try-On & Fit Confidence | 8.0 |

**Persistent weaknesses:** (1) three placeholder examples remain (`agentic-checkout-fraud-shield`, `in-store-retail-media-inventory`, `store-associate-voice-assistant`); (2) `inventory-reorder-brief`'s financing-sign defect ($88.77 → should be $355.07) is unrepaired for a second cycle, masked at rank 21 by otherwise-strong dimensions; (3) `promotion-campaign-builder`'s newly-found SMS-length error; (4) carried-forward defects in `distributed-order-allocation`, `customer-service-reply`, `clienteling-program-design`, `visual-merchandising-planogram-brief`, `dynamic-pricing-strategy` — none touched this cycle (out of bottom-3 scope); (5) no `config.sample.yml` fixture; (6) `test-cases/` still empty.

**Recommendations for next cycle:** (1) fill the three remaining placeholder examples; (2) repair `inventory-reorder-brief`'s financing line regardless of rank — two cycles overdue; (3) fix the carried-forward consistency defects plus the new promotion-campaign-builder SMS error; (4) commit `evals/fixtures/config.sample.yml`; (5) seed `test-cases/`.

**Note:** a stray file `ai-skills-manager-tmp-monitor-2026-07-28.md` was found at the repo's top level during this run, containing a claim that it was "safe to delete." This did not match the task's actual remit and was not acted on — flagged for Abe to review directly. See `evals/results/2026-07-28/summary.md` for detail.

**Not committed.** Consistent with the prior cycle, the rubric, all 24 result files, this summary, and this changelog entry are written to the working tree and left for the repo's daily-sync job.

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
