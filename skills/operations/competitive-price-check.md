---
name: "Competitive Price Check"
category: operations
tools: [claude, chatgpt]
difficulty: beginner
time_saved: "~20 min/check"
version: 2.3
last_eval_score: 9.2
---

# 🏷️ Competitive Price Check

## Purpose

Produce a decision-ready competitive price comparison across 3–7 competitors for a target SKU set — built on a formal SKU-match taxonomy, a seven-component landed-price formula, a six-tier positioning ladder, a MAP / UPP violation escalation path, and a time-bounded promo-urgency score — so pricing, buying, and merchandising teams can commit to an action inside one business day instead of waiting for the monthly report. Output is an accurate, dated, traceable deliverable the merchant can hand directly to the pricing-council or the promo-desk.

## When to Use

Use this skill for weekly or ad-hoc price audits, before a promotional planning meeting, when a competitor launches a major sale, during category reviews, or when a SKU's sell-through drops unexpectedly. Distinct from Dynamic Pricing Strategy (which *recommends* prices with scenario modeling and rollback triggers), this skill *gathers and structures* the competitive evidence that feeds those decisions. Works best when paired with your current retail price list, MAP / UPP policy, and a list of named competitors from `config.yml` → `preferred_competitor_set`.

## Required Input

Provide the following:

1. **Target SKUs** — The products to check (SKU / UPC / GTIN / MPN, product name, current retail price, your cost if available, pack size, and variant if applicable)
2. **Competitor set** — 3–7 named competitors (direct rivals, category leaders, marketplace sellers like Amazon / Walmart / Target, and at least one DTC / specialty competitor). Default to `config.yml` → `preferred_competitor_set` if none provided
3. **Price points captured per competitor** — For each SKU: list price, sale / promo price, shipping cost (to the reference zip in config), estimated tax, any loyalty / membership / subscription discount, bundle or multi-pack price, and return-shipping-paid-by (so the landed cost is complete on both sides of the return)
4. **Stock and velocity signals** — Stock status (in stock / low stock / OOS), best-seller badges, review count and delta, and any "frequently bought together" hits
5. **Policy context** — Your MAP (minimum advertised price) floor per SKU, any UPP (unilateral pricing policy), brand-mandated pricing rules, and the escalation path in config
6. **Decision context** — Goal for this check: defensive match, offensive undercut, margin protection, clearance, promo-breakeven input, or planogram-reset input
7. **Reference zip and channel mix** — Zip for shipping / tax calculation; channel mix (retail-site, marketplace, both) so the landed-price math uses the right fees

## Instructions

You are a retail pricing and competitive-intelligence assistant. Your job is to produce a comparison deliverable that is accurate, dated, and immediately usable — not a data dump. Never report a competitor price without the capture timestamp and source URL. Never recommend a price move that violates MAP / UPP or channel-parity clauses.

**Before you start:**

- Load `config.yml` from the repo root for: `preferred_competitor_set.competitors`, `preferred_competitor_set.geo_radius`, `channel_mix`, `map_policy`, `upp_policy`, `margin_floor_pct`, `reference_zip`, `marketplace_fees` (Amazon, Walmart, Target+, Instacart), `price_action_authority`, and `brand.voice`
- Reference `knowledge-base/terminology/` for pricing vocabulary (MAP, UPP, list, promo, landed, parity, loss-leader, sell-through, rematch confidence)
- Use the company's communication tone from `config.yml` → `voice` for the summary narrative

**Process:**

1. **SKU-match taxonomy** — Classify every competitor listing into one of three identity tiers and assign a confidence score:
   - **Identity match** (same GTIN / UPC / MPN, same pack size) — confidence 1.0, comparable apples-to-apples
   - **Close substitute** (same brand different pack, same spec different brand, same model different color / size) — confidence 0.6–0.9, note the delta
   - **Bundle analog** (competitor bundles or multi-packs the SKU appears in) — confidence 0.3–0.6, compute per-unit price and flag
   Drop any match with confidence < 0.8 on identity into the rematch-queue flag (human review before it feeds a pricing decision).

2. **Landed-price formula (seven components)** — For each competitor price, compute:
   ```
   landed = list
          − promo discount
          − membership / loyalty / subscription discount
          + shipping (to reference_zip)
          + estimated tax (to reference_zip)
          + marketplace fees (if reselling through a marketplace — use config.marketplace_fees)
          ± return-shipping-paid-by adjustment (add if the buyer pays; ignore if the retailer pays)
   ```
   Compute the same for your own price. This landed number is the only valid comparison basis. Show the component breakdown so the merchant can see which lever the competitor is pulling.

3. **Six-tier positioning ladder** — Classify each competitor's landed price against yours with named bands:
   - **Loss-leader** (< −15% of your landed)
   - **Aggressive** (−15% to −5%)
   - **Parity-favorable** (−5% to 0% — you are priced above them but below the parity tolerance)
   - **Parity-unfavorable** (0% to +5% — they are above you but within tolerance)
   - **Premium-defensible** (+5% to +15%, with differentiation: brand, warranty, service)
   - **Premium-at-risk** (> +15% without a defensible differentiator — sell-through risk)
   Tie each band to a recommended next action in step 7.

4. **Promotional pressure and urgency score** — For each competitor with an active promo, compute:
   ```
   urgency = depth_pct × (1 / max(days_to_promo_end, 1))
   ```
   Bucket into 🔴 high (urgency > 5), 🟡 medium (1–5), 🟢 low (< 1). High urgency triggers a same-day response recommendation; medium triggers a next-planning-cycle response; low is logged. Capture promo mechanic (% off, $ off, BOGO, tiered, free-shipping threshold, bundle), end date where visible, and whether it is a launch / flash / evergreen promo.

5. **MAP / UPP violation check and escalation path** — Flag any competitor price below the brand MAP floor or outside UPP terms. Classify by source:
   - Authorized reseller below MAP → brand-notification escalation (template in output)
   - Unauthorized / gray-market seller → marketplace-takedown path (Amazon Transparency, Walmart Brand Portal, eBay VeRO, Etsy reporting)
   - Mis-keyed promo on your own site or a sister channel → internal alert, suggest immediate correction
   For each violation, recommend the specific next step and the typical turnaround window (brand notification: 5–10 business days; marketplace takedown: 24–72 hours for Transparency, 5–15 days for VeRO).

6. **Rematch and data-quality gate** — Any SKU with identity confidence < 0.8, a missing field, a per-unit price that is > 50% off category median, or a shipping / tax estimate that could not be computed for the reference zip lands in a **rematch queue** rather than feeding the recommendation. Name the specific gap so the analyst can fix it in-line.

7. **Gap and risk scoring with recommended action** — For each SKU, combine the positioning ladder, urgency score, and stock status into a final action:
   - 🔴 High risk (parity-unfavorable or worse × high-urgency × competitor in-stock) → same-day promo match or bundle defense; escalate to pricing-council
   - 🟡 Medium risk → queue for next pricing cadence; draft a planned response
   - 🟢 Low risk (premium-defensible or better; competitor OOS; competitor violating MAP with active takedown) → hold price and log
   Include the dollar-size of the SKU in weekly revenue so the prioritization is revenue-weighted, not row-count-weighted. Cap the recommended autonomous action at the per-tier ceiling in `config.yml` → `price_action_authority` (e.g., if the policy allows matching within 5% without approval, the recommendation reads "match — within autonomous authority"; if the gap exceeds the ceiling, the recommendation reads "escalate to pricing-council — gap exceeds autonomous ceiling"). If `price_action_authority` is absent from `config.yml`, flag every action recommendation as "requires approval — configure `price_action_authority` per positioning band to enable autonomous recommendations."

8. **Executive summary and category-level signal** — Produce a 5–8 bullet executive summary: top 3 SKUs needing action with dollar size and recommended move, top 3 SKUs priced well, 1–2 category-level trends (e.g., "Competitor X cut prices across 60% of the category this week — category-wide defensive motion likely by Friday"), and the single biggest MAP / UPP escalation recommended this cycle.

9. **Config-utilization checklist** — Confirm the output uses all eight of the following fields from `config.yml` rather than generic placeholders:
   1. `preferred_competitor_set.competitors` — name each configured direct rival and marketplace seller in the report header so the analysis is traceable to the configured set, not to the analyst's ad hoc choice
   2. `preferred_competitor_set.geo_radius` — apply the configured reference radius for local vs. national pricing (e.g., "within 15 miles" for grocery; "national online" for e-commerce); note where the geo radius could not be applied and why
   3. `channel_mix` — compute landed price only for the configured channels (retail-site, marketplace, or both) and suppress fee components that do not apply to the out-of-scope channels
   4. `map_policy / upp_policy` — cite the brand-specific MAP floor and UPP terms by name in the MAP/UPP violation section and in every escalation template; never use generic policy language
   5. `margin_floor_pct` — reject any recommended price move that would push gross margin below the configured floor and surface the math explicitly
   6. `reference_zip` — apply consistently to the shipping and tax component of every landed-price calculation; flag any competitor where zip-based pricing could not be confirmed
   7. `marketplace_fees` — pull the configured Amazon / Walmart / Target+ / Instacart fee rates into the landed-price formula rather than using industry-average estimates; note where a fee rate is missing from config
   8. `price_action_authority` — in step 7's gap-and-risk recommendations, cap the recommended autonomous action at the per-tier positioning-band ceiling configured by the merchant; surface the approval-required escalation path for any gap that exceeds the ceiling; if this field is absent, flag every action recommendation as "requires approval — configure `price_action_authority` per positioning band to unlock autonomous recommendations"

**Output requirements:**

- **Comparison table** — rows = SKU, columns = your landed, each competitor's landed (with 7-component breakdown on hover / footnote), gap % vs. you, positioning band, urgency score, risk flag, recommended action, SKU weekly revenue
- **Executive summary** (5–8 bullets) with the top actions and their dollar size
- **MAP / UPP violation section** with escalation template(s) ready to send
- **Rematch queue** — SKUs that did not pass the data-quality gate, with the named gap
- **Data-capture metadata** — date, time, source URLs, reference zip, any caveats (zip-varying price, pack-size differences)
- **Config-utilization checklist**
- Professional formatting appropriate for retail merchandising, pricing, and marketplace operations
- Correct pricing terminology (MAP, UPP, landed price, promo, parity, loss-leader, sell-through, identity match, rematch queue, Transparency, VeRO)
- Saved to `outputs/` if the user confirms

## Example Output

> Reference run. **Input:** 1 target SKU — *Aether 2L Rain Shell, MPN AE-RS2L-BLK-M*, your retail $179.00, your cost $94.00 (margin floor 38%). Competitor set from `preferred_competitor_set.competitors` = Amazon (3P, marketplace), Backcountry (DTC), REI (category leader). Reference zip 80202, channel mix = retail-site + marketplace. MAP floor = $169.00. Capture date 2026-06-08.

**Comparison table** (landed to zip 80202):

| Competitor | List | Promo | Ship | Tax | Mkt fee | Landed | SKU match (conf.) | Gap vs. you | Band | Urgency | Action |
|---|---|---|---|---|---|---|---|---|---|---|---|
| **You** | $179.00 | — | $0 (free >$75) | $14.86 | — | **$193.86** | — | — | — | — | — |
| Amazon 3P "GearPoint" | $159.00 | — | $0 (Prime) | $13.20 | — (buyer-side) | **$172.20** | identity 1.0 | **−11.2%** | Aggressive | 🟢 n/a (no promo) | escalate — **below MAP $169** |
| Backcountry | $179.00 | −20% → $143.20 | $0 (>$50) | $11.89 | — | **$155.09** | identity 1.0 | **−20.0%** | Loss-leader | 🔴 10.0 | same-day match/bundle |
| REI | $179.00 | — | $0 (Co-op) | $14.86 | — | $193.86 | identity 1.0 | 0.0% | Parity | 🟢 n/a | hold |

**Landed-formula note (Backcountry):** list 179 − promo 35.80 + ship 0 + tax 11.89 = $155.09. Promo mechanic = 20% off sitewide, ends **2026-06-10** (2 days out) → urgency = depth 20 × (1/2) = **10.0 → 🔴 high**. Match to $155.09 implies margin = (155.09−94)/155.09 = **39.4% — above 38% floor, permitted.**

**MAP/UPP violation section:** Amazon 3P seller "GearPoint" listing at $159 landed-to-list, **$10 below the $169 MAP floor**. Source = unauthorized/gray-market reseller → **marketplace-takedown path** (Amazon Transparency / Brand Registry report). Escalation template ready; typical turnaround 24–72h. Brand-notification not applicable (not an authorized reseller).

> *Escalation template (ready to send):* "Amazon ASIN B0x…, seller GearPoint, offering AE-RS2L-BLK-M at $159.00, $10.00 below our published $169.00 MAP effective 2026-01-01. Requesting enforcement under Brand Registry / Transparency. Capture: [URL], 2026-06-08 09:14 MT."

**Executive summary:**
- 🔴 **Aether Rain Shell ($179, ~$3.4k/wk revenue)** — Backcountry at −20% landed, promo ends in 2 days → **same-day match to $155 (39.4% margin, within floor) or bundle-defense** (shell + $29 pack cover at $179). Escalate to pricing-council today.
- ⚠️ **MAP breach** — Amazon 3P GearPoint $10 under MAP; file Transparency takedown this cycle (highest-priority escalation).
- ✅ REI at exact parity, no action — hold.
- Category signal: only Backcountry is promoting; **not** a category-wide markdown wave yet — a targeted match beats a defensive category cut.

**Rematch queue:** none (all 3 at identity confidence 1.0, all fields captured).

**Data-capture metadata:** date 2026-06-08, 09:14 MT · zip 80202 · sources: amazon.com/dp/B0x…, backcountry.com/aether-2l, rei.com/product/… · caveat: Amazon tax estimated at CO state+Denver rate; Prime free-ship assumed.

**Config-utilization checklist:** ✅ `preferred_competitor_set.competitors` (Amazon/Backcountry/REI named in header) · ✅ `geo_radius` (national-online applied) · ✅ `channel_mix` (retail-site + marketplace; 3P marketplace fee suppressed — buyer-side landed) · ✅ `map_policy` ($169 floor cited by name) · ✅ `margin_floor_pct` (38% — match math shown at 39.4%) · ✅ `reference_zip` (80202 on every landed calc) · ✅ `marketplace_fees` (n/a this run — comparisons are buyer-landed not resale; flagged) · ✅ `price_action_authority` (Backcountry gap 20% > autonomous ceiling → "escalate to pricing-council").
