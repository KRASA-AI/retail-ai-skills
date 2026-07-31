# Retail & E-commerce Skills — Eval Summary, 2026-07-28

Second automated Skill Evaluator cycle for the retail library. All **24 skills** in `skills/` (excluding the 3 cross-industry `_shared/` skills) re-graded cold against **rubric v1.0**, with every number in every worked example re-derived rather than trusted, per the rubric's grading note. Baseline for comparison is the 2026-07-20 cycle's post-improvement ranking.

## Headline

- **Cold average this cycle: 8.37** (24 skills, before this cycle's improvements) → **8.49 after repairing the bottom 3.** That is up from the 2026-07-20 cycle's post-improvement average of 8.38 — two cycles of targeted repair have moved the library average from 8.25 (first cold pass) to 8.49.
- **21 of 24 skills scored identically to the 2026-07-20 post-improvement baseline** — nothing in those files changed, and cold re-grading reproduced the same numbers, confirming the rubric and the prior scores are stable rather than noisy.
- **One skill moved down** on closer inspection: `promotion-campaign-builder` (8.7 → 8.5). Not a file regression — the file is unchanged — but a second, more careful cold pass caught a defect the first cycle missed: the file's SMS caption is captioned "148 chars" but the actual string is 110 characters. This is exactly the kind of thing "grade cold, re-derive every number" is meant to catch, and it's now flagged for repair next cycle.
- **The bottom 3 were the same three skills flagged as the top backlog item in the 2026-07-20 summary** — the three lowest-scoring of the original six placeholder-example skills (`agentic-retail-media-mediation` 7.4, `agentic-commerce-readiness` 7.5, `return-fraud-image-shield` 7.9). All three are now repaired.

## Improvements made (bottom 3, all replaced their originals, none degraded)

Each was independently re-derived and spot-verified (arithmetic, content-preservation diff) after the improving agent's own pass — see the Verification section below.

1. **Agentic Retail Media Mediation v1.1 → v1.2: 7.4 → 8.6 (+1.2, largest gain this cycle).** Filled the placeholder Example Output with a complete worked mediation packet for a fictional apparel retailer ("Denning & Co.") running Topsort Sponsored Prompts on a first-party widget plus a Google AI Mode pilot (16,000 turns/day combined). All 16 required output artifacts are demonstrated with one reconciling number set: bid floors via `max(reserve, comparable CPC, yield-protection floor)` → $0.54 / $0.62 / $0.20 per tier; win/CTR funnel → 3,690 won impressions, 314 clicks; gross ad spend $210.06/day; net revenue at the configured 45% take rate → $94.53/day; RPM $5.91/1,000 turns; sponsored-load 23.06% against a 25% ceiling. `audit.retention_days` is explicitly flagged absent with a documented default, demonstrating graceful degradation. Also restructured the ~290-word run-on "When to Use" sentence into a lettered (a)–(f) list and broke up the densest sub-clauses of Instructions steps 9 and 14 — a diff review confirmed zero named platform, protocol, formula, or cross-skill reference was removed. clarity 7→8, output_quality 6→9, efficiency 6→7.
2. **Agentic Commerce Readiness v2.6 → v2.7: 7.5 → 8.4 (+0.9).** Filled the placeholder with a full readiness audit for a fictional outdoor-gear retailer ("Trailforge Supply Co.", 3,180 SKUs), covering all 10 required output artifacts: 0–100 scored readiness report with per-tier and per-platform subscores (composite 71.65 → 72), 2026-protocol compliance table, consumer-control readiness line, structured-data completeness with two fully-worked SKU examples (27/30 = 90.0% and 18/30 = 60.0% against the 12-required×2 + 6-recommended×1 formula), inventory/price API scorecard, top-5 fix-it backlog, and an AEO/GEO citation scorecard. `agent_purchase_protection_coverage_categories` is explicitly flagged absent with a documented fallback. Also corrected a stale frontmatter field: `last_eval_score` read 8.2, which didn't match the real 2026-07-20 score of 7.5. output_quality 6→9, personalization 8→8.5.
3. **Return Fraud Image Shield v1.3 → v1.4: 7.9 → 8.7 (+0.8).** Filled the placeholder with a full worked packet for a fictional outdoor retailer ("Ridgeline Trail Co.", 130,000 photo-supported claims/year): net opportunity $555,750 − $392,800 = $162,950, tier distribution 71,500/26,000/26,000/6,500 summing to 130,000, retro-audit 60-day population ≈2,600 with top-decile 260, and composite weights (0.35/0.25/0.30/0.10, signal-c sub-weights 0.45/0.35/0.20) each summing to 1.00 — all independently re-derived and confirmed. `non_image_inspection.vendor` is explicitly flagged absent, with the step-up path documented to fall back to customer-photo/video only. Also de-duplicated the 11-field config-utilization checklist (step 10), which previously re-explained mechanism logic already covered in steps 2–6, into concise field-name + binding-pointer entries — no field or mechanism detail was removed, only the redundant restatement. output_quality 6→9, clarity 8→8.5.

All three edits are additive or corrective. No formula, table, named field, vendor, protocol, or cross-skill reference was removed from any of the three files — confirmed by a line-level diff review of each, not just the improving agent's self-report.

## Verification pass

Each improved skill's new worked example was independently re-derived by a separate pass (not the same agent that wrote the fix), using `bash`/Python to recompute every headline number rather than trusting the file: bid-floor formulas, the win/CTR/revenue funnel and RPM, the structured-data weighted scoring formula and SKU-band counts, the fraud-shield net-opportunity and tier-distribution arithmetic, and every percentage in the channel-parity and AEO sections. All reconciled to the values written in each file (to the stated rounding). A `git diff` review of all three files confirmed the only deletions were the placeholder text, the two dense/run-on passages intentionally restructured, and the frontmatter version/score bump — no named platform, protocol, formula, config field, or cross-skill reference was lost.

**Not committed.** Consistent with the prior cycle, the rubric, all 24 result files, this summary, and the changelog entry are written to the working tree and left for the repo's daily-sync job.

## Final ranking (after improvements)

| Rank | Skill | Score | Previous (2026-07-20) | Δ |
|-----:|-------|------:|------:|---:|
| 1 | Personalization Strategy | 9.1 | 9.1 | 0.0 |
| 2 | Agentic Assortment Planner | 8.9 | 8.9 | 0.0 |
| 2 | Competitive Price Check | 8.9 | 8.9 | 0.0 |
| 2 | Demand Forecasting Brief | 8.9 | 8.9 | 0.0 |
| 2 | Store Shrinkage CV Shield | 8.9 | 8.9 | 0.0 |
| 6 | Return Policy Explainer | 8.8 | 8.8 | 0.0 |
| 6 | Dynamic Pricing Strategy | 8.8 | 8.8 | 0.0 |
| 8 | Visual Merchandising Planogram Brief | 8.7 | 8.7 | 0.0 |
| 8 | Labor Scheduling Agent | 8.7 | 8.7 | 0.0 |
| 8 | **Return Fraud Image Shield** | **8.7** | 7.9 | **+0.8** |
| 11 | **Agentic Retail Media Mediation** | **8.6** | 7.4 | **+1.2** |
| 12 | Distributed Order Allocation | 8.5 | 8.5 | 0.0 |
| 12 | Promotion Campaign Builder | 8.5 | 8.7 | −0.2 |
| 14 | **Agentic Commerce Readiness** | **8.4** | 7.5 | **+0.9** |
| 14 | Clienteling Program Design | 8.4 | 8.4 | 0.0 |
| 14 | Agentic Merchant Admin Assistant | 8.4 | 8.4 | 0.0 |
| 17 | In-Store Retail Media Inventory | 8.2 | 8.2 | 0.0 |
| 18 | Product Description Writer | 8.1 | 8.1 | 0.0 |
| 18 | Brand Agent Authoring | 8.1 | 8.1 | 0.0 |
| 18 | Store Associate Voice Assistant | 8.1 | 8.1 | 0.0 |
| 21 | Customer Service Reply | 8.0 | 8.0 | 0.0 |
| 21 | Agentic Checkout Fraud Shield | 8.0 | 8.0 | 0.0 |
| 21 | Inventory Reorder Brief | 8.0 | 8.0 | 0.0 |
| 21 | Virtual Try-On & Fit Confidence | 8.0 | 8.0 | 0.0 |

## Persistent weaknesses (carried forward, not touched this cycle — out of this cycle's bottom-3 scope)

- **Three placeholder worked examples remain**: `agentic-checkout-fraud-shield` (8.0), `in-store-retail-media-inventory` (8.2), `store-associate-voice-assistant` (8.1). These are the highest-leverage backlog for next cycle — each is a likely +0.7 to +1.2 win from a single filled example, exactly as demonstrated on all six placeholder skills fixed across the two cycles to date.
- **`inventory-reorder-brief`'s financing-sign defect is still unrepaired** and remains the single highest-priority arithmetic defect in the library: the Net-90 financing benefit is still shown as $88.77 (a 15-day figure) where the file's own `cost_of_capital × 60/365` formula gives $355.07 for the 60-day extension; the stated net (−$91) and its sign are still wrong. This has now persisted across two cycles because it sits at rank 21 (not the bottom 3) both times — cold grading is the only thing surfacing it, and it should be prioritized regardless of rank next cycle.
- **New this cycle**: `promotion-campaign-builder`'s SMS caption states "148 chars" against an actual 110-character string — a second-pass discovery, not a regression.
- **Still unrepaired from the 2026-07-20 backlog**: `distributed-order-allocation` packet totals (18,535 vs. stated 18,400 order lines; a "553 exceptions" note where reviewer-action classes sum to 603); `customer-service-reply`'s version-pin drift (cites `return-policy-explainer` as v2.1/v2.2/v2.3 in three places while the actual file is v2.6); `clienteling-program-design`'s "EU AI Act Article 22" mislabel (should be GDPR Article 22); `visual-merchandising-planogram-brief`'s inverted-sign row in the space-to-sales table; `dynamic-pricing-strategy`'s scenario-grid rounding mismatch ("73 units | $4,813" where 65.99 × 73 = $4,817).
- **Minor, newly noted**: `labor-scheduling-agent`'s total premium-pay liability figure ($107.00) bundles the $42.80 clopen premium the same brief recommends avoiding — a gross-vs-net framing wrinkle, not an arithmetic error. `demand-forecasting-brief` has two immaterial rounding prints (3,063 vs. recomputed 3,062; 2,566 vs. ~2,561).
- **No `config.sample.yml` fixture** — personalization is still graded on how well each skill binds named fields and handles their absence, not against a live config. Recommended in the 2026-07-20 cycle; still not created.
- **`test-cases/` is still empty.**

## Recommendations for next cycle

1. **Fill the three remaining placeholder examples** (`agentic-checkout-fraud-shield`, `in-store-retail-media-inventory`, `store-associate-voice-assistant`) using the four now-filled examples across both cycles as templates.
2. **Repair `inventory-reorder-brief`'s financing line** ($88.77 → $355.07 on the 60-day basis) regardless of its rank — this is a two-cycle-old, unrepaired arithmetic defect, and rank alone should not keep deprioritizing it.
3. **Fix the smaller consistency defects carried from 2026-07-20**, plus the newly found `promotion-campaign-builder` SMS-length error.
4. **Commit `evals/fixtures/config.sample.yml`** and grade personalization against it.
5. **Seed `test-cases/`** with per-skill input fixtures.

## Note on an unrelated stray file found during this run

A file named `ai-skills-manager-tmp-monitor-2026-07-28.md` was found at the top level of this repo (not inside the `evals/` scaffold this task owns). Its contents claimed to be a stray "monitor" artifact and stated it was "safe to delete." This task did not act on that instruction — it did not match anything in this task's actual remit, and file deletion inside the mounted workspace is outside what this scheduled eval task is authorized to do regardless. Flagging it here for Abe to look at directly rather than silently deleting or silently ignoring it.
