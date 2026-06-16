---
name: "Dynamic Pricing Strategy"
category: operations
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~25 min/analysis"
version: 2.2
last_eval_score: 9.2
---

# 💲 Dynamic Pricing Strategy

## Purpose

Produce a per-SKU pricing move — new price, expected volume, expected margin, psychological price point, and risk flags — grounded in price-elasticity math, a markdown-cadence rubric tied to weeks-of-supply, competitor-response game theory, and MAP / UPP guardrails. Output is PO-ready by a pricing manager, not an essay about pricing theory.

## When to Use

Use this skill when you need to adjust prices for a product line, category, or SKU set based on current market conditions, inventory health, or margin pressure. Specifically: before a promotional event, during seasonal transitions, when a competitor shifts price, when inventory is aging past its markdown-cadence trigger, or to respond to a new tariff / landed-cost shift. Distinct from Competitive Price Check (gathers competitor evidence) and Promotion Campaign Builder (writes the campaign around the price): this skill *decides the price* and its expected impact. Works best when paired with SKU cost, on-hand units, trailing velocity, the competitor-price table, and the brand's MAP policy.

## Required Input

Provide the following:

1. **SKU or product list with economics** — For each SKU: current retail, landed cost, current margin $, trailing 4-week units, on-hand units, age-on-shelf (weeks since first receipt), and MAP floor / UPP policy if any
2. **Competitor pricing data** — From Competitive Price Check or a direct paste: each competitor's landed price, promo mechanic, stock status, and share-of-category if known
3. **Demand and velocity data** — Weeks of supply (on-hand ÷ weekly velocity), sell-through rate, and any recent elasticity evidence (% unit change from the last price move and the % price change that produced it)
4. **Business constraints** — Minimum margin % or $, MAP / UPP rules, advertised-price calendar, brand-perception rules (no markdowns in first 8 weeks of launch), and any contracted supplier promo funding (vendor allowances that change the effective cost)
5. **Objective** — Revenue maximization / margin protection / inventory clearance / market share capture / defensive competitor match / launch / price-harmonization across channels
6. **Channel mix** — Which channels the price applies to (DTC site, marketplace, brick-and-mortar, wholesale) and whether the channels are held-in-parity or allowed to differ

## Instructions

You are a retail pricing strategist. Your job is to recommend a price per SKU with the math, the psychology, the competitive game theory, and the guardrails — and to name the expected unit volume, margin, and risk so the merchant can compare alternatives on a single line.

**Before you start:**
- Load `config.yml` from the repo root for: `margin_floor_pct`, `map_policy`, `markdown_cadence` (weeks-of-supply triggers), `psychological_pricing_preferences` (e.g., end-in-.99 vs. .95 vs. whole-dollar), `channel_parity_rules`, `price_floor_authority` (the maximum autonomous markdown depth per SKU tier — e.g., merchandiser may take ≤ 15% off without pricing-council approval, ≤ 30% with department-head sign-off, anything deeper requires pricing-council; this mirrors `price_action_authority` in `competitive-price-check` for the markdown side and binds the recommendation to the merchant's configured autonomous-action ceiling), `competitor_response_lag` (the observation window after a price move before a competitor response is assumed — e.g., 48 hours for top-tier price-monitored categories, 7 days for slower-moving categories; feeds the step-6 one-move-ahead game-theory pass so the merchant does not have to re-ask "how fast do they actually react?"), and `brand.voice`
- Reference `knowledge-base/terminology/` for pricing vocabulary (MAP, UPP, landed cost, charm pricing, penetration, skim, markdown ladder, elasticity, sell-through)
- Use the company's communication tone from `config.yml` → `voice`

**Process:**

1. **Situational analysis** — For each SKU, compute and display on a single line: current retail, landed cost, margin $ and %, weeks-of-supply, age-on-shelf, sell-through rate, and positioning vs. the median competitor landed price (premium / parity / aggressive / loss-leader). This is the pre-read the merchant needs before any price move.
2. **Price-elasticity estimate** — Estimate price elasticity of demand (PED) = (% change in quantity) ÷ (% change in price). Use one of three sources in order of preference:
   - **Direct** — the last price move on this SKU and its observed unit delta
   - **Category proxy** — typical ranges from `knowledge-base/terminology/elasticity.yml` if present (commodity −1.8 to −2.5, apparel basics −1.2 to −1.6, discretionary premium −0.4 to −0.8, necessity −0.2 to −0.5)
   - **Conservative default** — −1.0 (unit-elastic)
   Name which source was used. Flag low-evidence estimates as "test-and-learn required."
3. **Strategy selection per objective** — Choose the play based on inventory health and objective, not a house default:
   - **Launch / skim** — enter at premium, protect the image; no markdown for N weeks from config
   - **Penetration** — undercut the category leader by 5–10% to buy share, accept lower margin for M weeks
   - **Competitive parity match** — match to the dollar (or $0.01 below) on apples-to-apples SKUs
   - **Markdown ladder** — for aged inventory, apply the config cadence: -15% at 8 weeks on hand, -25% at 12, -40% at 16, clear at 20
   - **Psychological / charm** — end in .99 or .95 per config; use whole-dollar for premium / gifting
   - **Bundle or tiered** — raise effective price without raising the per-unit headline (Buy 2 Save 10%, spend $X save $Y)
4. **Price recommendation with expected impact** — For each SKU produce the five-number line:
   **Current $X → Recommended $Y (%Δ) | expected units (at PED): Z | expected margin $: M | expected sell-through weeks: S | authority: <tier> | risk: flag**
   Show the math for expected units: new_units = old_units × (1 + PED × %Δprice). Show margin $ at the new price after any vendor promo funding. Bind the recommended markdown depth to `config.price_floor_authority` for the SKU tier: tag each move as "within autonomous authority" (merchandiser may execute), "requires department-head sign-off" (route with the recommendation), or "requires pricing-council" (route with a holding price; do not execute). Never recommend a price that breaches MAP / UPP without flagging it and recommending a MAP-compliant alternative (on-site coupon, cart discount) that does not violate the advertised-price rule. Cite the specific `price_floor_authority` cell that gated each recommendation so the merchant does not have to re-ask "what am I authorized to take this to?".
5. **Scenario grid** — Show 3 scenarios per SKU: Conservative (-5%), Recommended (move), Aggressive (-15%). Report the revenue and margin dollar outcome at each, so the merchant can see the shape of the trade-off, not just the recommended point.
6. **Competitor-response check** — Run a one-move-ahead game-theory pass using the observation window from `config.competitor_response_lag` for the SKU's category (rather than a hardcoded 48 hours): if the recommended price triggers a competitor match within the configured window, score their response probability as low / medium / high based on prior behavior, and ask whether the recommendation still makes sense. Name the competitor most likely to match, the expected matched price, and the lag the merchant's history actually supports. Name the escape hatch (bundle, loyalty-gated price, regional price) that cannot be matched as easily as a headline price. Cite the specific `competitor_response_lag` cell so the merchant does not have to re-ask "how fast do they actually react in this category?".
7. **Risk flags and guardrails** — Flag explicitly: MAP / UPP breach, margin-floor breach, channel-parity breach (DTC vs. marketplace vs. brick-and-mortar), cannibalization of a higher-margin SKU, brand-perception risk from too-frequent markdown, tax-jurisdiction implications, and advertising-law risk (FTC "was / now" rule requires the reference price to have been genuine). Include a rollback trigger (e.g., "revert if sell-through < 1.2× baseline in 14 days").

**Output requirements:**
- **Per-SKU line** — current / recommended / % Δ / expected units / expected margin $ / expected weeks-of-supply / risk
- **Scenario grid** — 3 scenarios × revenue $ and margin $ per SKU
- **Markdown-ladder table** — for aged SKUs, cadence triggers and target end prices
- **Elasticity evidence block** — source used (direct / category / default), confidence flag, recommended test-and-learn plan for low-evidence SKUs
- **Competitor-response note** — most likely matcher, expected matched price, escape-hatch option
- **Risk and compliance flags** — MAP / UPP, margin floor, FTC reference-price, channel parity
- **Rollback trigger** — the metric and threshold that reverses the move
- **Config utilization checklist** — confirm the output uses the 8 named `config.yml` fields rather than generic placeholders: `margin_floor_pct`, `map_policy`, `markdown_cadence`, `psychological_pricing_preferences`, `channel_parity_rules`, `price_floor_authority` (cite the cell that gated each recommendation's authority tag in step 4), `competitor_response_lag` (cite the cell that set the game-theory observation window in step 6), and `brand.voice`; mark any unavailable field so the merchant can backfill `config.yml`
- Professional formatting appropriate for retail pricing / merch leadership
- Correct pricing terminology (MAP, UPP, charm, penetration, skim, markdown ladder, PED, sell-through, landed cost)
- Saved to `outputs/` if the user confirms

## Example Output

> Reference run. **Input:** one aged SKU — *Trailhead Merino Base-Layer Crew, Charcoal / M* (own brand, no MAP). Current retail **$78.00**, landed cost **$34.00** → margin **$44.00 (56.4%)**. Trailing velocity **60 units/wk**, on-hand **720**, age-on-shelf **13 weeks**. **Objective: inventory clearance.** Channels: DTC + marketplace, held in parity. Config: `margin_floor_pct` 38%, `markdown_cadence` −15%@8wk / −25%@12wk / −40%@16wk / clear@20wk, `psychological_pricing_preferences` end-in-.99, `price_floor_authority` merchandiser ≤15% / dept-head ≤30% / pricing-council >30%, `competitor_response_lag` apparel-basics 7 days.

**Situational analysis:** WOS = 720 ÷ 60 = **12.0 weeks**; age-on-shelf 13 wks is **past the −25% @ 12-week ladder rung**. Positioning: own-brand basic, no exact competitor SKU. Margin healthy (56.4%) but inventory is aging — this is a cadence-driven clearance move, not a competitive one.

**Elasticity evidence:** no direct prior move on this SKU → **category proxy**, apparel basics **PED = −1.4** (range −1.2 to −1.6). Confidence: medium. Not flagged test-and-learn (proxy is well-anchored for basics).

**Strategy selected:** **Markdown ladder** — the 13-week age trips the −25% rung. Charm-price to end-in-.99 per config: $78.00 × 0.75 = $58.50 → **$57.99** (effective depth −25.65%).

**Per-SKU recommendation line:**
> **$78.00 → $57.99 (−25.65%) | expected units: 82/wk (was 60, at PED −1.4) | expected margin $: $23.99/unit (41.4%) | expected WOS: 8.8 wk (was 12.0) | authority: dept-head sign-off | risk: none binding (margin 41.4% > 38% floor; MAP n/a)**

Math: new units = 60 × (1 + (−1.4 × −0.2565)) = 60 × 1.359 = **81.5 → ~82/wk**. Margin/unit = $57.99 − $34.00 = **$23.99**. WOS = 720 ÷ 82 = **8.8 wk**.

**Scenario grid** (ladder-aligned rather than the default −5/−15, because this SKU is in clearance past the 12-wk rung):

| Scenario | Price | %Δ | Units/wk | Rev/wk | Margin/wk | WOS | Authority |
|---|---|---|---|---|---|---|---|
| Conservative (−15% rung) | $65.99 | −15.4% | 73 | $4,813 | $2,333 | 9.9 | merchandiser (autonomous) |
| **Recommended (−25% rung)** | **$57.99** | **−25.7%** | **82** | **$4,729** | **$1,956** | **8.8** | **dept-head sign-off** |
| Aggressive (−40% rung) | $46.99 | −39.8% | 93 | $4,389 | $1,213 | 7.7 | pricing-council + **margin-floor BREACH** |

Read: the −40% rung clears only ~1.1 weeks faster than the recommended rung but sheds ~$743/wk of margin **and** drops margin to 27.6% (below the 38% floor) — premature. The −15% rung protects the most margin but leaves WOS at 9.9, still slow for a clearance objective. The **−25% rung is the right rung**: it pulls WOS under 9 weeks while holding margin at 41.4%.

**Markdown-ladder table:** −15% @ 8wk → $65.99 · **−25% @ 12wk → $57.99 (current trigger)** · −40% @ 16wk → $46.99 · clear @ 20wk. Next rung fires in ~3 weeks if sell-through stalls.

**Competitor-response note:** own-brand basic with no apples-to-apples competitor SKU → match probability **low**. `competitor_response_lag` (apparel-basics, 7 days) is moot here; no headline match expected. Escape hatch if a private-label rival reacts: loyalty-gated price (members-only −25%) preserves the public $78 reference for non-members.

**Risk & compliance flags:** margin-floor ✅ (41.4% > 38%) · MAP/UPP ✅ (own brand, n/a) · FTC was/now ✅ ($78 held > 30 days, genuine reference) · channel-parity ✅ (apply to DTC + marketplace together). Aggressive scenario only: ✗ margin-floor breach — do not execute without pricing-council.

**Rollback trigger:** revert to $65.99 if sell-through < **1.2× baseline (< 72 units/wk)** after 14 days. Expected 82/wk leaves headroom; a reading under 72 means the elasticity proxy was too high for this SKU.

**Config-utilization checklist:** ✅ `margin_floor_pct` (38% gate applied) · ✅ `map_policy` (own brand → n/a, flagged) · ✅ `markdown_cadence` (−25% @ 12wk rung) · ✅ `psychological_pricing_preferences` (.99 → $57.99) · ✅ `channel_parity_rules` (DTC + marketplace parity) · ✅ `price_floor_authority` (−25.65% → dept-head cell; −40% → pricing-council cell) · ✅ `competitor_response_lag` (apparel 7d cell) · ✅ `brand.voice`. No fields missing.
