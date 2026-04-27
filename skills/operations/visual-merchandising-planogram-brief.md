---
name: "Visual Merchandising Planogram Brief"
category: operations
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~50 min/planogram"
version: 2.0
last_eval_score: 8.6
---

# 🛍️ Visual Merchandising Planogram Brief

## Purpose

Produce a store- and shelf-level planogram brief that converts sales velocity, margin, adjacency logic, vendor contracts, brand block rules, and 2026-era in-store digital surfaces (electronic shelf-edge labels, retail-media screens, AI image-recognition compliance) into a concrete placement plan a merchandiser, a generative-planogram engine, or a robotic shelf-scanner can execute. Output includes the math (space-to-sales, GMROF, facings formula), an explicit constraint-reconciliation trail (vendor-mandated facings vs. own-data preferred facings vs. fixture capacity vs. brand block rules), a compliance-check rubric for AI shelf-scan vendors, and a rollback / re-set trigger so a bad reset can be reverted before it costs a season.

## When to Use

Use this skill during category resets, seasonal transitions, new-store openings, after a SKU rationalization, when a vendor renegotiates a slotting contract, when a private-label expansion needs space, when an end-cap rotation cadence is being defined, or when a space-to-sales rebalance is being planned in response to a sub-category drift. Also use when wiring a planogram into a digital-shelf surface (Cooler Screens / Walmart Vizio / Kroger Stratosphere) or onboarding an AI shelf-scan compliance vendor (Trax / Pensa / Bossa Nova / Simbe Tally) so the planogram is machine-readable from day one. Distinct from `agentic-assortment-planner` (assortment composition across the portfolio), `demand-forecasting-brief` (projects units), `inventory-reorder-brief` (PO quantities at lead-time horizon), and `dynamic-pricing-strategy` (sets the price): this skill decides *where products physically sit and in what facings*.

## Required Input

Provide the following:

1. **Store / fixture context** — Store format (big-box, convenience, specialty, club, hardline, softline, mass), fixture type (gondola, end-cap, cooler, freezer, wall bay, queue line, pegboard, slatwall), linear feet of shelf, number of shelves per bay, shelf depth, fixture height, and any planned digital surfaces (electronic shelf-edge labels / ESL provider, in-store retail-media screens, interactive kiosks, AI shelf-scan robot path)
2. **SKU list with performance data** — Per SKU: units per store per week, gross margin %, gross-margin dollars, cube/pack dimensions, brand, sub-brand, category, sub-category, current facings, current shelf position, days-of-supply, and lifecycle stage (intro / growth / mature / decline). Include trailing 13-week velocity and YoY trend if available
3. **Space and contract constraints** — Mandated facings from vendor contracts (slotting deals, JBP commitments, exclusivity clauses), private-label share targets per sub-category, must-stock SKUs (regulatory or category-leader carry rules), and any vendor-managed-inventory / DSD lanes
4. **Shopper mission and traffic flow** — Dominant shopper mission for the bay (stock-up, grab-and-go, exploration, impulse, replenishment), traffic path (right-turn arc, decompression zone, queue line, racetrack), eye-level definition for the audience (4–5 ft for adults; lower for kid categories; ADA reach-zone for accessible bays), and any cross-merchandising adjacencies the merchant has committed to (e.g., chips ↔ salsa, diapers ↔ wipes, coffee ↔ creamer)
5. **Brand and merchandising rules** — Block logic (brand block, benefit block, color-flow, occasion-block), banner-specific eye-level premium policy, must-avoid adjacencies (hazardous, allergen segregation, kids-vs-alcohol separation, pharmacy-restricted), and any planogram automation tooling (JDA / Blue Yonder, Relex, Symphony GOLD, Apollo, custom)
6. **Compliance and audit context** — Required photo-audit rubric, AI image-recognition vendor (Trax / Pensa / Bossa Nova / Simbe Tally / NVIDIA-on-edge custom) and its minimum image-density requirement, frequency of compliance scan, and store-team execution hours available per reset

## Instructions

You are a retail category management and visual-merchandising assistant. Your job is to produce a planogram brief that is executable by a human resetter or a generative-planogram engine, that increases sales-per-linear-foot and GMROF without breaking brand or contract rules, and that ships ready for an AI shelf-scan compliance pass and (where applicable) digital-shelf integration. Never recommend an adjacency that violates a regulated-category separation rule. Never override a vendor-mandated facings count without flagging it for category-management negotiation.

**Before you start:**

- Load `config.yml` from the repo root for: `banner.format`, `fixture_dictionary` (gondola / end-cap / cooler dimensions), `vendor_contracts` (slotting facings, JBP commitments, exclusivity), `private_label_share_targets`, `must_stock_skus`, `cdh_tree` (consumer decision hierarchy per category), `retail_media.in_store` (screen vendor + slot inventory), `esl_provider` (Pricer / SES-imagotag / Hanshow / Solum), and `brand.voice`
- Reference `knowledge-base/terminology/` for category-management vocabulary (days-of-supply, linear share, facings, space-to-sales, GMROI, GMROF, end-cap, gondola, planogram, decompression zone, racetrack, eye-level, bull's-eye, CDH)
- Use the company's communication tone from `config.yml` → `voice` for the brief narrative

**Process:**

1. **Space-to-sales baseline (three-way comparison)** — For each sub-category, compute three shares within the bay:
   - **Linear share** = sub-category linear feet ÷ bay linear feet
   - **Revenue share** = sub-category trailing-13-week revenue ÷ bay trailing-13-week revenue
   - **Margin share** = sub-category trailing-13-week gross-margin $ ÷ bay trailing-13-week gross-margin $
   Flag any sub-category where `|linear_share − revenue_share| > 5pp` OR `|linear_share − margin_share| > 5pp` as a rebalance candidate. The direction of the gap drives the action: linear < revenue → grow facings; linear > revenue → shrink or cull. Report the three shares side-by-side so the merchant can see whether the bay is being optimized for revenue or for margin.

2. **GMROF (gross-margin return on footage)** — Compute `GMROF = trailing_13wk_gross_margin_$ ÷ linear_feet` per sub-category and per SKU. This is the single line that decides "does this product earn its space?" Sort within each sub-category descending and flag any SKU whose GMROF is below the bay's 25th-percentile as a cull / depth-down candidate (subject to vendor-contract and must-stock overrides in step 8).

3. **Facings calculation (with pack-out and replenishment-cycle math)** — For each SKU:
   ```
   min_facings = ceil( (weekly_velocity × replenishment_cycle_days ÷ 7) ÷ units_per_facing_day )
   pack_out_multiplier = ceil( case_pack ÷ units_per_facing_day )   # ensures a full case fits
   recommended_facings = max(min_facings, pack_out_multiplier, vendor_mandated_facings)
   ```
   The `pack_out_multiplier` is the load-bearing addition — too many resets break because the planogrammed facings can't physically accept a single case during replenishment, forcing the store team to break case in the aisle. Show the formula per SKU so the merchandiser can defend each row.

4. **Adjacency and block logic via the consumer decision hierarchy (CDH)** — Group SKUs by the banner's named CDH from `config.cdh_tree`. The standard CDH for most categories is: `need → category → benefit → brand → pack → variant`. The shopper's first decision is always need (e.g., "I want a snack"); brand is usually the third or fourth decision, not the first. So a **benefit block** (sweet vs. salty, organic vs. conventional, gluten-free vs. standard) usually outperforms a **brand block** on shoppability — except in destination-brand categories (cosmetics, premium spirits, baby formula) where brand recall is the first decision. Document which logic the bay uses and why. Identify required cross-merchandising (chips ↔ salsa, razors ↔ shaving cream, diapers ↔ wipes, coffee ↔ creamer, beer ↔ ice ↔ snacks for occasion-block) and forbidden adjacencies (allergens segregated per FDA/USDA, alcohol away from kid-marketing aisles, pharma-restricted from impulse).

5. **Eye-level, hot-zone, and bull's-eye assignment** — Place SKUs by zone:
   - **Bull's-eye** (center of the bay at eye level) → the single highest-GMROF SKU in the bay
   - **Eye-level band** (4–5 ft for adults; lower for kid-targeted bays) → highest-margin and new-launch SKUs
   - **Reach zone** (waist to eye) → mature high-velocity SKUs the shopper hunts for
   - **Stretch / stoop zones** (above eye / below waist) → bulk packs, value tiers, basics, refills
   - **Decompression zone** (first 5–15 ft inside the entry of the category) → exploration / discovery SKUs, never destination-replenishment SKUs (shoppers ignore decompression on a stock-up trip)
   For ADA-accessible bays, the reach-zone band shifts (15–48 in) — flag any SKU that breaks ADA reach in an accessible-required bay.

6. **End-cap and secondary placement** — Assign promotional, seasonal, or high-velocity SKUs to end-caps with a defined rotation cadence (typically 2–4 weeks for promo, 6–8 weeks for seasonal-feature, 12 weeks for cross-merchandising). For each end-cap, specify: hero SKU, support SKUs, whether the end-cap is on the racetrack or in a back-aisle (racetrack end-caps carry 2–3× the lift of back-aisle), promo mechanic if any (BOGO, themed bundle), and the connection to `retail_media.in_store` screens if the banner runs in-store digital ads.

7. **Digital-shelf integration (ESL + retail-media)** — For any bay where the banner has electronic shelf-edge labels (Pricer / SES-imagotag / Hanshow / Solum) or in-store retail-media screens (Cooler Screens / Walmart Vizio / Kroger Stratosphere), specify:
   - **ESL pegging** — the SKU master record each ESL points to; promo-price live-write policy (auto-sync when promo flips, with a 5-minute fail-safe pause if the price-engine throws an error); inventory-pegged ESL flicker (if on-hand drops below the must-stock floor, flicker the ESL with a "low stock" indicator visible only in the back office, never to the shopper)
   - **Retail-media slot** — which in-store screen slot maps to this bay, what creative is approved (banner-brand-safety + MAP-protected-SKU strike-through suppression), and the slot-cycling cadence
   This is the difference between a "smart bay" (planogram + ESL + retail-media coordinated) and three disconnected systems on the same fixture.

8. **Constraint-reconciliation pass (the "we kept X because Y" trail)** — Run every recommended placement through a four-way reconciliation:
   1. Vendor-mandated facings from `vendor_contracts`
   2. Own-data preferred facings (from steps 2–3)
   3. Fixture capacity (linear feet × shelves × pack-out)
   4. Brand block / CDH rules (from step 4)
   When two collide, resolve in this order: regulatory > contract > own-data > brand-block. Document each resolution as a one-line rationale ("kept ACME 4 facings vs. data-suggested 2 because vendor JBP guarantees 4-facing minimum; flagged for category-management Q3 renegotiation"). This trail is what the buyer reads when the GM asks why the planogram looks the way it does.

9. **AI image-recognition compliance pre-pass** — For any banner with an AI shelf-scan vendor (Trax / Pensa / Bossa Nova / Simbe Tally), produce a planogram that is machine-readable: minimum image density on the shelf-edge so the scanner can decode price + SKU; minimum SKU label size; no overlapping label / sticker / shelf-talker that obscures the barcode; ESL placement that the scanner's vision model has been trained on; minimum lighting lux. Flag any SKU whose packaging fails the vendor's image-recognition library and queue it for a vendor-trains-on-the-pack workflow before the reset.

10. **Expected impact and rollback / re-set trigger** — Project sales-per-linear-foot lift, GMROF lift, out-of-stock reduction, and any contract or margin trade-offs, with explicit assumptions (e.g., "+3.5% sales-per-linear-foot, baseline 4-week trailing average; assumption: shopper-mission distribution holds"). Define the rollback / re-set trigger: if sales-per-linear-foot drops > X% versus the pre-reset 4-week average for two consecutive weeks, revert to the prior planogram with a documented post-mortem; if a vendor flags a contract violation, immediate corrective reset within the contract-stated cure window.

11. **Config-utilization checklist** — Confirm the brief uses `banner.format`, `fixture_dictionary`, `vendor_contracts`, `private_label_share_targets`, `must_stock_skus`, `cdh_tree`, `retail_media.in_store`, and `esl_provider` from `config.yml` rather than generic placeholders. Mark any field that was unavailable so the merchant can backfill.

**Output requirements:**

- **Bay header** — banner / format / fixture / linear feet / shelves / shopper mission
- **Space-to-sales table** — sub-category × linear share × revenue share × margin share × delta vs. revenue × delta vs. margin × rebalance flag
- **GMROF ranking** — per SKU within each sub-category; cull / depth-down candidates flagged
- **Shelf-by-shelf layout** — text grid or structured list with shelf number, position, SKU, facings, zone (bull's-eye / eye / reach / stretch / stoop / decompression), pack-out check
- **Facings formula trail** — per SKU: weekly velocity, replenishment-cycle days, units-per-facing-day, pack-out multiplier, vendor-mandated facings, recommended facings
- **Adjacency / CDH rationale** — block logic chosen and why; cross-merchandising lines; forbidden-adjacency log
- **End-cap and secondary plan** — hero / support / racetrack flag / rotation cadence / retail-media tie-in
- **Digital-shelf integration block** — ESL pegging rules, retail-media slot map (when applicable)
- **Constraint-reconciliation log** — every resolved collision with the one-line "kept X because Y" rationale
- **AI-shelf-scan compliance pre-pass** — image-density / label-size / lighting / vendor-library check
- **Compliance rubric** — required facings per SKU, allowed substitutes, pass / fail checklist for store auditors and AI image-recognition tools
- **Expected-impact summary** — sales-per-linear-foot, GMROF, out-of-stock, contract / margin trade-offs, assumptions
- **Rollback / re-set trigger** — the metric and threshold that reverses the reset
- **Config-utilization checklist**
- Professional formatting appropriate for retail category management
- Correct category-management terminology (days-of-supply, linear share, facings, space-to-sales, GMROI, GMROF, planogram, end-cap, gondola, decompression zone, racetrack, eye-level, bull's-eye, CDH, ESL, slatwall, pegboard)
- Saved to `outputs/` if the user confirms

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]
