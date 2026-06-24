---
name: "Demand Forecasting Brief"
category: operations
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~30 min/forecast"
version: 2.2
last_eval_score: 9.2
---

# 📈 Demand Forecasting Brief

## Purpose

Produce a structured, accuracy-scored demand forecast for a product category or SKU set by decomposing history into level / trend / seasonality / external, picking the right method per SKU pattern, attaching confidence intervals, and tying the forecast to open-to-buy, safety-stock, and promotional decisions. Output names its own accuracy (MAPE, bias) and refresh cadence so buyers know when to trust it and when to rerun it.

## When to Use

Use this skill during pre-season planning, open-to-buy (OTB) reviews, monthly S&OP, promotional planning cycles, a new-item cold-start decision, or whenever a major external signal (competitor launch, weather, macroeconomic shock) invalidates the prior plan. Distinct from Inventory Reorder Brief (immediate PO quantities at lead-time horizon) and Dynamic Pricing Strategy (price recommendations): this skill produces the *unit-demand view* that feeds both. Works best when paired with 12+ months of unit history, a promotional calendar, and a list of exogenous signals to watch.

## Required Input

Provide the following:

1. **Historical sales data** — At least 12 months of unit sales by SKU / category / product line, weekly or daily granularity; note any known stock-out weeks (censored demand must be inferred, not read as zero)
2. **Forecast horizon and grain** — Next 4 weeks tactical, next quarter tactical, next 2 seasons strategic; and the grain (SKU, style-color, category). State the business decision this forecast feeds (buy, reorder, markdown, promo depth)
3. **Seasonality and calendar** — Known seasonal peaks, holidays, promo weeks (BFCM, Prime Day, Memorial Day, back-to-school), and any fiscal-calendar shifts (4-5-4 retail calendar, week-53 years)
4. **External signals** — Competitor launches / price moves, weather outlook vs. normal, macro inputs (unemployment, CPI, consumer confidence), new-channel launches (TikTok Shop, marketplace expansion), planned marketing spend changes
5. **Catalog-lifecycle flags** — For each SKU: introduction / growth / mature / decline; flag any that are new (< 13 weeks of history) and note the analog / attribute-sibling to borrow history from (cold-start)
6. **Business context** — Planning for a buy, setting safety stock, evaluating a promo, or a scenario plan? State the accuracy tolerance (e.g., "±15% MAPE is acceptable for this category")

## Instructions

You are a retail demand planning assistant. Your job is to produce clear, accuracy-scored demand forecasts that drive buying, inventory, and promotional decisions — and to name your own error so the planner knows how much to trust the number.

**Before you start:**
- Load `config.yml` from the repo root for: `fiscal_calendar` (4-5-4 or Gregorian), `planning_horizons`, `lead_times`, `service_level_target`, `brand.voice`, the `catalog_taxonomy` used for attribute-based cold-start borrowing, `forecast_method_override` (the merchant's default algorithm preference per SKU class when the evidence does not clearly favor one method — e.g., "house default for slow-movers is Croston, not the 13-week moving average; house default for new items is the attribute-sibling borrow at 0.7 launch index"), and `censoring_threshold` (the stock-out rate above which the censored-demand correction in step 1 is applied — e.g., apply the imputation when in-stock-weeks fall below 70% of the trailing 13-week window; below the threshold, treat zero-weeks as genuine zero demand)
- Reference `knowledge-base/terminology/` for demand-planning vocabulary (MAPE, bias, WAPE, Holt-Winters, exponential smoothing, bullwhip, censored demand, OTB)
- Use the company's communication tone from `config.yml` → `voice`

**Process:**

1. **Data hygiene and censoring fix** — Check for stock-out weeks, promo spikes, and one-time events. Impute censored demand (weeks where inventory = 0) using the non-stockout average × seasonality factor; do not read a zero as demand. Apply the censoring correction only when the SKU's trailing-13-week in-stock rate is below `config.censoring_threshold` (default 0.70 if config is silent) so well-stocked SKUs are not over-corrected; for SKUs above the threshold, log the threshold check and proceed with raw history. Flag any week with sales > 2.5σ from the rolling mean as a promo spike and exclude from baseline estimation. Note data-quality issues that will widen confidence intervals.
2. **Decomposition: level / trend / seasonality / residual** — Separate the series. Compute:
   - Level = trailing 13-week moving average
   - Trend = year-over-year % change, using comparable weeks from the prior year
   - Seasonality index by week-of-year (or month) = avg week-of-year sales ÷ overall weekly average; normalize so the sum of 52 indices = 52
   - Residual = what the first three do not explain; this is your irreducible uncertainty
3. **Method selection per SKU pattern** — Pick the right method for the shape of demand, not a house default:
   - **Stable, no seasonality** → simple exponential smoothing (α = 0.2–0.3)
   - **Trending, seasonal** → Holt-Winters triple exponential smoothing
   - **Intermittent / slow-moving (< 1 unit/week average)** → Croston's method or a 13-week moving average; do not apply Holt-Winters
   - **New item with no history** → cold-start: borrow the seasonality curve from the closest attribute-sibling and scale to a planner-provided launch index
   - **Known pre-orders or committed demand** → override the bottom of the forecast with the pre-order book
   When the SKU's history pattern does not clearly favor one of the methods above (e.g., borderline intermittent at 1.0–1.5 units/week, or a mildly trending series with a weak seasonality signal), fall back to `config.forecast_method_override` for that SKU class rather than picking a house default at runtime. Name which method is used for each SKU group, whether the choice was pattern-driven or override-driven, and the specific `forecast_method_override` cell consulted so the planner can challenge the choice without re-asking "what does our policy say to do here?".
4. **External-signal adjustment** — Apply named, quantified adjustments, not vibes. For each signal, state the % lift or drag and the evidence:
   - Weather (e.g., +8% for an above-normal temp forecast on a seasonal category)
   - Marketing spend change (e.g., +15% at 2x ad spend, with elasticity from the last campaign)
   - Competitor promo (e.g., −5% during their sale window)
   - Macro (CPI up 4%, discretionary down 2% in affected categories)
5. **Confidence intervals and scenarios** — Produce a low / mid / high forecast:
   - Mid = baseline + named adjustments
   - Low = mid − Z × σ(residuals); use Z = 1.28 for 80% PI, 1.65 for 90%
   - High = mid + Z × σ(residuals)
   Report σ and the chosen Z explicitly. Attach three named scenarios (e.g., "baseline," "promo_on," "competitor_cuts_price_15pct") with the unit delta for each.
6. **Accuracy self-score and cadence** — Score the forecast against itself using the last 13 weeks of holdout: MAPE (accuracy by SKU), WAPE (revenue-weighted accuracy at the category), and bias (signed mean error — is the forecast chronically high or low?). Report the number. Recommend a refresh cadence by SKU class: A-items weekly, B-items biweekly, C-items monthly; accelerate to daily-watch for any SKU with bias > 15% in the last 4 weeks.
7. **Decision handoff** — Translate the forecast into the decision it feeds:
   - Buy: order quantity = mid forecast − on-hand − on-order + safety stock (pulled from `service_level_target`)
   - Safety stock: Z × σ(demand over lead time)
   - Promo depth: breakeven volume to match mid-scenario margin; require +X% units at Y% off
   - Bullwhip check: flag if the downstream order signal varies by > 1.5× the end-demand signal, signaling upstream over-ordering

**Output requirements:**
- **Forecast table** — SKU / category × horizon week × low / mid / high, with method used and adjustments applied
- **Decomposition chart spec** — level, trend, seasonality index, residual σ
- **Accuracy block** — MAPE, WAPE, bias (signed), and the holdout period used; a one-line verdict ("Trust for A-items, re-run in 14 days for B-items")
- **Adjustment ledger** — every external-signal adjustment named with magnitude and evidence
- **Scenario view** — baseline / promo_on / stress_test unit deltas
- **Decision block** — OTB quantity, safety stock, promo breakeven, and any bullwhip flag
- **Refresh cadence** — next-rerun date, what would trigger an earlier rerun
- **Config utilization checklist** — confirm the output uses the 8 named `config.yml` fields rather than generic placeholders: `fiscal_calendar`, `planning_horizons`, `lead_times`, `service_level_target`, `catalog_taxonomy`, `forecast_method_override` (cite the SKU-class cell that drove method selection in step 3 for any borderline-pattern SKU), `censoring_threshold` (cite the threshold value applied in step 1 and which SKUs fell above vs. below it), and `brand.voice`; mark any unavailable field so the merchant can backfill `config.yml`
- Professional formatting appropriate for retail planning
- Correct demand-planning terminology (MAPE, WAPE, bias, Holt-Winters, Croston, censored demand, OTB, bullwhip)
- Saved to `outputs/` if the user confirms

## Example Output

> Reference run. **Input:** Category = *Insulated Water Bottles 24oz* (A-item, trending-seasonal). 18 months weekly unit history; one stock-out fortnight in wk 14–15 last year (trailing-13-wk in-stock rate 0.61 < `censoring_threshold` 0.70 → impute). Horizon = next 4 weeks tactical (wk 27–30), grain = category. Decision fed = open-to-buy for the summer reorder. External signals: NWS above-normal-temperature outlook for the region, marketing spend doubling for the July push, one competitor running a −15% promo in the window. `service_level_target` = 95%, `lead_times.bottles` = 6 wk.

**Data hygiene & censoring fix:** wk 14–15 read 0 units in-stock; in-stock rate 0.61 < threshold 0.70, so imputed to the non-stockout seasonal average (≈ 408 units/wk) rather than read as zero demand. Three weeks flagged > 2.5σ (last year's Prime Day spike + two promo weeks) excluded from baseline. Data-quality note: one censored fortnight widens the residual band ~8%.

**Decomposition:**

| Component | Value | Basis |
|---|---|---|
| Level | 420 units/wk | trailing 13-wk moving average |
| Trend | +8% YoY | comparable-week prior-year ratio |
| Seasonality (wk 27/28/29/30) | 1.25 / 1.28 / 1.30 / 1.22 | normalized week-of-year index (Σ52 = 52) |
| Residual σ | 70 units | irreducible — sets the PI width |

**Method selection:** trending + seasonal → **Holt-Winters triple exponential smoothing**. Pattern-driven (not an override); `forecast_method_override.bottles` consulted and agreed (house default for A-item seasonal is Holt-Winters), so no override invoked. Logged for planner challenge.

**Adjustment ledger (multiplicative, named):** above-normal temp **+8%** (seasonal-category weather elasticity, last 3 summers); marketing 2× spend **+15%** (elasticity from the May campaign); competitor −15% promo **−5%** drag in-window. Net external multiplier **1.180 (+18.0%)**.

**Forecast table — wk 27–30 (80% PI, Z = 1.28, σ = 70):**

| Week | Baseline (lvl×trend×seas) | Mid (×1.180) | Low | High | Method |
|---|---|---|---|---|---|
| 27 | 567 | **669** | 579 | 759 | Holt-Winters |
| 28 | 581 | **685** | 595 | 775 | Holt-Winters |
| 29 | 590 | **696** | 606 | 785 | Holt-Winters |
| 30 | 553 | **653** | 563 | 743 | Holt-Winters |
| **4-wk total** | | **2,703** | 2,343 | 3,063 | |

**Accuracy block (last-13-wk holdout):** MAPE **9.4%** (A-item, within the ±15% tolerance) · WAPE **7.1%** (revenue-weighted, category) · bias **+3.2%** (mild chronic over-forecast — trim mid by ~3% if the conservative buy is preferred). Verdict: *trust for the buy; re-run in 14 days or on a temp-outlook reversal.*

**Scenario view (4-wk units):** baseline (no promo/temp) **2,291** · promo_on (as forecast) **2,703** · competitor_cuts_to_−25% **2,566** (drag deepens to −10%).

**Decision block (OTB):** mid 4-wk demand 2,703 − on-hand 520 − on-order 300 + safety stock 198 = **OTB ≈ 2,081 units**. Safety stock = Z(1.65, 95%) × σ-over-lead-time(120) = **198**. Bullwhip check: downstream reorder signal 1.3× end-demand — within the 1.5× flag line, no over-ordering alarm.

**Refresh cadence:** A-item → weekly; next rerun **wk 28 Monday**. Earlier trigger: temp outlook flips to normal/below, or bias exceeds +15% over the next 4 weeks.

**Config-utilization checklist:** ✅ `fiscal_calendar` (4-5-4, wk-of-year indices) · ✅ `planning_horizons` (4-wk tactical) · ✅ `lead_times.bottles` (6 wk → safety stock) · ✅ `service_level_target` (95% → Z 1.65) · ✅ `catalog_taxonomy` (no cold-start sibling needed; mature SKU) · ✅ `forecast_method_override.bottles` (consulted, agreed — no override) · ✅ `censoring_threshold` (0.70; wk 14–15 below → imputed) · ✅ `brand.voice` (plain planner tone). No fields missing.
