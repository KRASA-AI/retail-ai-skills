---
name: "Dynamic Pricing Strategy"
category: operations
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~25 min/analysis"
version: 2.1
last_eval_score: 8.0
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

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]
