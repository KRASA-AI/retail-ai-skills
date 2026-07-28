---
name: "Agentic Assortment Planner"
category: operations
tools: [claude, chatgpt]
difficulty: advanced
time_saved: "~60 min/cycle"
version: 1.1
last_eval_score: null
---

# 🧭 Agentic Assortment Planner

## Purpose

Move a buying team from quarterly batch assortment reviews to a continuous, agent-orchestrated replan loop. Output is a weekly (or daily, in fast fashion / grocery) assortment-decision packet: which SKUs to add, hold, mark down, exit, or reallocate across stores and channels — with the productivity-per-square-foot, overlap, whitespace, and margin-at-risk evidence each call rests on. The skill does not replace the buyer; it produces the structured packet a buyer can approve, edit, or reject in one sitting and pushes the approved deltas into the assortment system through the merchant's existing PIM / merchandising stack.

## When to Use

Use this skill when (a) the merchant is moving from cadence-based merchandising (quarterly buys, monthly reviews) to always-on replans, (b) a category's contribution margin has slipped two periods running and the buy team needs a SKU-level diagnosis between formal reviews, (c) an event upstream — supplier delay, weather break, viral demand spike, competitive markdown — invalidates the current allocation, (d) a new store, channel, or pop-up needs an opening assortment derived from comparable doors, or (e) the planning team wants whitespace recommendations grounded in their own attribute taxonomy rather than vendor-pitched gaps. Distinct from `demand-forecasting-brief` (unit demand projection feeding a buy), `inventory-reorder-brief` (PO quantities at lead-time horizon), `dynamic-pricing-strategy` (price moves on the assortment in place), and `competitive-price-check` (per-SKU competitive reference): this skill is the *assortment composition and allocation* call across a portfolio of SKUs.

## Required Input

Provide the following:

1. **Assortment scope** — Banner / region / channel / store cluster the replan covers, the planning period (weekly / monthly / continuous-trigger), and whether the run is full-portfolio or restricted to a category, brand, or door cluster
2. **SKU master with attributes** — At minimum: SKU, parent style, brand, category path, attribute set (color, size, material, occasion, price tier, lifecycle stage, sustainability flag), introduction date, exit candidate flag, vendor-managed flag, exclusive flag
3. **Performance history** — Trailing 13 / 26 / 52 weeks of unit sales, gross margin, sell-through rate, weeks-of-supply, markdown depth, return rate, net margin after returns, by SKU × door (or door cluster)
4. **Space / planogram constraints** — Linear feet or facings allocated per door cluster per category, planogram refresh cadence, fixture types, minimum-presentation quantities, and any vendor-mandated facings (slotting deals)
5. **Demand and trend signals** — Output of `demand-forecasting-brief` for the period if available; otherwise a stated trend assumption per category. Include any external signals: weather outlook, viral / social signal, competitor entry / exit, regulatory change
6. **Whitespace / opportunity inputs** — Search-term gap data (your own site search "no results"), ChatGPT / AI-search query log if available, marketplace bestseller deltas, attribute combinations customers requested but you don't carry
7. **Financial and policy guardrails** — Open-to-buy budget by category, target gross margin, target inventory turn, maximum markdown depth, sustainability and ethical-sourcing rules, exclusivity / channel-parity contracts that constrain SKU placement
8. **Approval and write-back plan** — Who signs off (buyer / planner / merch director), what system the approved deltas write into (PIM, planogram tool, merchandising hub), and the rollback window if a delta turns out wrong

## Instructions

You are an always-on assortment co-pilot for a retail merchandising team. Your job is to surface SKU-level decisions weekly (or on-trigger) so the buyer spends time on judgment calls — not on assembling the evidence for them. Never fabricate a sales number or a margin figure: if a feed is missing or stale, flag the gap and downscope the recommendation. Never recommend exiting a SKU that is under a vendor-mandated facings contract or an exclusivity clause without flagging the contract conflict to legal. Never recommend a markdown that violates a price-parity clause from `config.yml → parity_rules`.

**Before you start:**

- Load `config.yml` from the repo root for: `fiscal_calendar`, `door_clusters`, `category_taxonomy`, `attribute_dictionary`, `open_to_buy`, `service_level_target`, `parity_rules`, `vendor_contracts`, and `brand.voice`
- Reference `knowledge-base/terminology/` for assortment-planning vocabulary (sell-through, GMROI, weeks-of-supply, productivity-per-foot, depth vs. breadth, replenishment vs. seasonal, basic vs. fashion, end-cap, planogram)
- If `demand-forecasting-brief` output is available, ingest it as the unit-demand baseline; otherwise compute a naive seasonally-adjusted run-rate and label it as such
- Use the company's communication tone from `config.yml → voice` for the narrative

**Process:**

1. **Door clustering check** — Confirm the door clusters in the input match `config.yml → door_clusters`; if a store has changed cluster (new format, remodel, demographic shift) flag it before running per-cluster math, since cluster mismatch is the single most common source of a wrong allocation. Reuse the cluster definition consistently across the run.

2. **Per-SKU lifecycle classification** — Tag each SKU as Introduction (< 13 weeks of history), Growth (sell-through accelerating, > 60th-percentile within attribute peer set), Mature (stable sell-through within ±10% week-over-week), Decline (sell-through falling two consecutive periods), or Exit-candidate (negative contribution margin after returns, or stranded inventory > 1.5× target weeks-of-supply with no recovery path). Lifecycle drives the action menu — Decline SKUs are markdown / exit candidates, Mature SKUs are reallocation candidates, Growth SKUs are depth-up candidates, Introduction SKUs are observation candidates.

3. **Productivity-per-foot diagnosis** — For each category × door cluster, compute revenue / square-foot and gross-margin / square-foot vs. cluster benchmark. Flag any category × cluster cell more than one standard deviation below benchmark; that is the cell where assortment changes have the most leverage. Avoid recommending changes in cells that are already at benchmark — cycle-time on those is wasted.

4. **Overlap audit** — Within each category, score attribute-overlap among active SKUs (color × size × price-tier × occasion). Two SKUs that share > 80% of attribute weight and have one of them under-indexing on sell-through are overlap candidates; one absorbs the other's demand, the loser is an exit candidate. Be careful with assortment depth in basics (overlap is a feature) vs. fashion (overlap is a leak).

5. **Whitespace surfacing** — Compare your active attribute combinations to (a) your own search-no-result terms, (b) marketplace bestseller attribute combos in your category, (c) any AI-search query log showing unbranded buying questions you can't answer. Score each whitespace candidate by estimated demand × confidence × adjacency to current capability (vendor exists, supply chain qualified, fits brand tone). Top candidates become add-recommendation entries; weak signals get logged for the next cycle, not pushed.

6. **Always-on action packet** — For each SKU within scope, produce one of: HOLD (no change), DEPTH_UP (raise units / facings, justified by sell-through), DEPTH_DOWN (cut units / facings), MARKDOWN (with depth %), REALLOCATE (move stock from cluster A to cluster B with named clusters and units), EXIT (with sell-down plan and write-down estimate), or ADD (new SKU candidate with vendor + first-buy units + door clusters). Each action carries: a one-line rationale, the dollar impact on contribution margin at the period horizon, the confidence band (high / medium / low based on data freshness and history length), and the constraint check (parity, vendor contract, planogram fit, OTB headroom).

7. **Constraint and guardrail pass** — Re-run every action against `config.yml → parity_rules`, `vendor_contracts`, `open_to_buy`, and the planogram-fit check. Drop or downscope any action that violates a constraint and replace it with the next-best non-conflicting action. Surface dropped-action conflicts as a separate "needs human policy decision" list, not silently.

8. **Continuous-replan trigger rules** — Define which signals would fire an off-cycle rerun before the next scheduled period: a stockout streak > N days on a Mature SKU, a sell-through delta > X% week-over-week on a Growth SKU, a vendor delay confirmed > Y days, a competitive markdown deeper than Z%, a weather outlook two standard deviations off normal in a weather-elastic category. Tune these against `config.yml → service_level_target` and the merchant's tolerance for re-papering store teams.

9. **Approval-ready packet assembly** — Group actions by buyer / planner owner, sort by absolute dollar impact, cap each owner's packet at a length they can review in one sitting (default: 25 actions / owner / cycle; spill the rest into a backlog with the same constraint-checked actions ready for the next cycle). Include a summary header (top 5 movers, top 5 risks, OTB consumed by approved actions if all are accepted).

10. **Write-back plan and rollback window** — Specify the system each approved action writes into (PIM for adds, planogram tool for facings, merchandising hub for markdowns, allocation engine for reallocations) and the rollback window — a recommended N-day check-back where the action is auto-flagged for review if the predicted lift hasn't materialized, with the data the buyer would need to roll it back cleanly. Never recommend an irreversible action (full SKU exit, vendor termination) without an explicit human approval gate.

11. **Config-utilization checklist** — Confirm the packet uses `door_clusters`, `category_taxonomy`, `attribute_dictionary`, `open_to_buy`, `parity_rules`, `vendor_contracts`, and `service_level_target` from `config.yml` rather than generic placeholders.

**Output requirements:**

- **Cycle header** — Banner / region / period / scope / data-freshness statement
- **Productivity-per-foot heat-map** — Category × door-cluster grid with under-benchmark cells highlighted
- **Per-SKU action packet** — Grouped by buyer / planner, capped at the per-owner length, sorted by dollar impact, each row: SKU, lifecycle, action, units / depth, dollar impact, confidence band, constraint check
- **Overlap audit** — Table of overlap-pair recommendations
- **Whitespace shortlist** — Top whitespace add-candidates with vendor / first-buy / door-cluster fit
- **Constraint conflict list** — Actions blocked by parity, vendor contract, planogram, or OTB — flagged for human policy decision
- **Continuous-replan trigger rules** — Signal definitions for off-cycle reruns
- **Write-back plan and rollback windows**
- **Config-utilization checklist**
- Professional formatting appropriate for retail merchandising leadership (not a vendor pitch)
- Correct merchandising terminology (sell-through, GMROI, weeks-of-supply, productivity-per-foot, depth vs. breadth, replenishment, end-cap, OTB, planogram, door cluster, lifecycle stage, attribute overlap)
- Saved to `outputs/` if the user confirms

## Example Output

> Worked example — a weekly replan on one category. Every number below is internally consistent and recomputable from the inputs shown; a real run substitutes the merchant's own feeds and `config.yml`.

### Cycle header

**Banner:** Northline Outfitters (140 doors, outdoor apparel & gear, ~$310M) · **Scope:** Women's Insulated Outerwear · **Period:** W28 FY26 (weekly cadence) · **Run type:** category-restricted, all door clusters
**Data freshness:** POS through Sat W27 (2 days old, within threshold) · attribute feed current · returns feed **6 days stale** → return-rate-dependent calls are labelled MEDIUM confidence, not HIGH
**Cluster check (step 1):** 4 clusters in input match `config.door_clusters` — Flagship Urban (12), Mall (48), Strip/Community (62), Outlet (18). ⚠️ Door #077 remodeled to Flagship format in W24 but is still tagged Mall. **Flagged before per-cluster math ran; excluded from cluster benchmarks this cycle** rather than silently mis-averaged.
**Demand baseline:** `demand-forecasting-brief` W27 output ingested (not a naive run-rate).

### Productivity-per-foot heat map (GM$/ft², trailing 13wk annualized)

| Door cluster | Category cell | Cluster benchmark | σ | z | Verdict |
|---|---|---|---|---|---|
| Flagship Urban | $431 | $402 | $44 | **+0.66** | At benchmark — no action, don't spend cycle-time here |
| Mall | **$268** | $358 | $46 | **−1.96** | 🔴 >1σ below — highest leverage |
| Strip/Community | $302 | $311 | $38 | **−0.24** | At benchmark |
| Outlet | **$214** | $289 | $41 | **−1.83** | 🔴 >1σ below — second-highest leverage |

Leverage is concentrated in **Mall** and **Outlet**. Flagship and Strip are at benchmark and get HOLDs, per step 3.

### Per-SKU action packet — Buyer: K. Nwosu (7 of 25-action cap; sorted by |$ impact|)

| SKU | Lifecycle | Action | Units / depth | $ impact (period) | Conf. | Constraint check |
|---|---|---|---|---|---|---|
| W-3308 Ridge Fleece Jacket, Heather | Growth (ST 78% @ wk9, 84th pct in peer set) | **DEPTH_UP** | +18 u/door × 48 Mall doors = **864 u** | **+$52,319 GM** | HIGH | OTB ✓ · parity ✓ · planogram ✓ (absorbs the 4 facings freed by W-4471) |
| W-4471 Alpine Down Parka, Cobalt | **Exit-candidate** (CM after returns **−$1,912**) | **EXIT** | Sell down 640 on-hand over 6 wks | **−$33,498 write-down**, stops −$1,912/period bleed, frees 4 facings | MEDIUM (stale returns feed) | Vendor contract ✓ (no mandated facings) · parity ✓ |
| W-5102 Packable Puffer Vest | Mature | **REALLOCATE** | 1,120 u **Flagship → Strip/Community** | +$18,400 GM (avoids markdown on stranded units) | HIGH | Flagship WOS 14.2 vs target 8.0; Strip WOS 5.1 — net-neutral to OTB |
| W-2214 Sherpa Shacket, **Sand** | Mature, **overlap 0.87** with W-2210 Oat | **DEPTH_DOWN → EXIT (Mall only)** | Cut to 0 facings in Mall; residual to Outlet | +$9,240 GM | HIGH | See overlap audit |
| W-4890 Trail Anorak, Neon | Decline (ST −2 periods) | **MARKDOWN 22%** | All doors | −$6,100 GM, clears $41K tied-up cost | HIGH | ⚠️ parity check: 22% is **inside** the `parity_rules` ceiling (25%) |
| *Petite-length insulated parka* | — | **ADD** (whitespace) | First buy 720 u, Mall + Flagship | +$31,900 GM (est., MEDIUM conf.) | MEDIUM | Vendor qualified (Cascade Peak) · OTB ✓ |
| W-6001 Basecamp Parka | Mature | **HOLD** | — | $0 | HIGH | Blocked from DEPTH_DOWN — see conflict list |

**Exit math shown (W-4471), since an EXIT is the one call a buyer will always challenge:** 1,180 gross units, 44% return rate → 519 returns, 661 net. Unit GM $61 ($189 retail − $128 landed — the buy came in 20 pts thin). Net-unit GM $40,321; reverse-logistics/inspect/repack 519 × $22 = $11,418; 208 returns resold at 45% off ($103.95 < $128 landed) = **−$5,002**; 311 liquidated at $45 = **−$25,813**. **Contribution margin after returns = −$1,912.** The SKU is not a markdown candidate — it is margin-negative *before* markdown, which is what distinguishes EXIT from MARKDOWN here. Sell-down: 640 on-hand at $128 basis ($81,920); staged 192 @ 40% off + 256 @ 60% off + 192 jobbed @ $38 recovers $48,422 → **$33,498 write-down**, taken once, versus an indefinite bleed.

### Overlap audit

| Pair | Attribute overlap | Sell-through | Verdict |
|---|---|---|---|
| W-2210 Sherpa Shacket **Oat** vs W-2214 **Sand** | **0.87** (color × size × price-tier × occasion) | Oat 67% / Sand **41%** | Sand is absorbed by Oat in Mall (fashion → overlap is a leak). Exit Sand from Mall; hold both in Outlet, where price-tier shoppers cross-shop colorways. |
| W-5102 Vest **Black** vs **Charcoal** | 0.91 | 58% / 55% | **No action** — basics, both replenishment. Overlap is a *feature* here (step 4). Deliberately not recommended for exit. |

### Whitespace shortlist

| Candidate | Signal | Est. demand × confidence × adjacency | Verdict |
|---|---|---|---|
| **Petite-length insulated parka** | 1,340 site-search no-results (13wk); marketplace bestseller combo; 2 vendor-qualified sources | High × High × High | **ADD** — first buy 720 u |
| Merino base-layer, plus-size | 310 no-results; no qualified vendor | Med × Med × **Low** | **Log for next cycle** — weak signal, not pushed |
| "Waterproof but breathable, under $150" (AI-search query cluster) | 87 unbranded buying questions | Med × Low × Med | **Log** — restate as an attribute-tagging fix, not a buy |

### Constraint conflict list — needs human policy decision (surfaced, not silently dropped)

- **W-6001 Basecamp Parka — DEPTH_DOWN blocked.** Under-indexing in Outlet, but `vendor_contracts` carries a **6-facing slotting mandate (Cascade Peak, through FY26 Q4)**. The action was dropped and replaced with HOLD; the conflict goes to merch legal / vendor management, not to the buyer's action queue. Downscoped alternative already prepared: renegotiate facings at the Q4 line review.

### Continuous-replan trigger rules (off-cycle rerun before next W29 cycle)

| Signal | Threshold | Fires |
|---|---|---|
| Stockout streak, Mature SKU | > 3 days | Immediate rerun, that cluster only |
| Sell-through delta, Growth SKU | > ±15% WoW | Rerun DEPTH_UP/DOWN on that peer set |
| Confirmed vendor delay | > 10 days | Rerun ADD + REALLOCATE |
| Competitor markdown | deeper than 25% on a matched SKU | Rerun MARKDOWN, route through `competitive-price-check` first |
| Weather outlook | ≥ 2σ off normal (outerwear = weather-elastic) | Rerun full category |

Thresholds tuned against `config.service_level_target` (97%) and the merchant's stated tolerance for re-papering store teams (**max 1 off-cycle rerun/month** — so the triggers are deliberately loose, not maximally sensitive).

### Write-back plan and rollback windows

| Action | Writes into | Rollback window | Gate |
|---|---|---|---|
| ADD (petite parka) | PIM | 14-day check-back: auto-flag if week-2 ST < 35% | Buyer approve |
| DEPTH_UP / DEPTH_DOWN | Planogram tool | Next planogram refresh (4 wks) | Buyer approve |
| MARKDOWN | Merchandising hub | 7-day: auto-flag if unit velocity lift < 1.5× | Buyer approve |
| REALLOCATE | Allocation engine | 10-day (transit + 3) | Auto (reversible) |
| **EXIT (W-4471)** | PIM + merch hub | **None — irreversible** | **Explicit human approval gate. Merch director sign-off required.** The agent does not exit a SKU. |

### Config-utilization checklist

`door_clusters` ✓ (4 clusters, mismatch on #077 flagged) · `category_taxonomy` ✓ · `attribute_dictionary` ✓ (overlap scoring) · `open_to_buy` ✓ (**$106,128 of $180,000 headroom = 59%** if all adds approved) · `parity_rules` ✓ (25% markdown ceiling) · `vendor_contracts` ✓ (Cascade Peak slotting mandate caught) · `service_level_target` ✓ (97%) · `brand.voice` ✓
**Unavailable / backfill:** `attribute_dictionary` has no `fit_length` attribute — which is why the petite whitespace had to be found through search logs instead of the taxonomy. **Backfill this before the next cycle**; it is the single field that would have surfaced the largest ADD candidate automatically.
