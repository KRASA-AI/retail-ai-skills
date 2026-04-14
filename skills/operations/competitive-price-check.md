---
name: "Competitive Price Check"
category: operations
tools: [claude, chatgpt]
difficulty: beginner
time_saved: "~20 min/check"
version: 2.0
last_eval_score: 4.6
---

# 🏷️ Competitive Price Check

## Purpose

Produce a structured competitive price comparison across 3–7 competitors for a target set of SKUs, surfacing landed-cost gaps, promotional pressure, and positioning risks — so pricing, buying, and merchandising teams can react within one business day instead of waiting on a monthly report.

## When to Use

Use this skill for weekly or ad-hoc price audits, before a promotional planning meeting, when a competitor launches a major sale, during category reviews, or when a SKU's sell-through drops unexpectedly. Distinct from Dynamic Pricing Strategy (which *recommends* prices with scenario modeling), this skill *gathers and structures* the competitive evidence that feeds those decisions. Works best when paired with your current retail price list and a list of named competitors.

## Required Input

Provide the following:

1. **Target SKUs** — The products to check (SKU / UPC / GTIN, product name, current retail price, your cost if available)
2. **Competitor set** — 3–7 named competitors (direct rivals, category leaders, marketplace sellers like Amazon/Walmart/Target, and at least one DTC / specialty competitor)
3. **Price points to capture per competitor** — At minimum: list price, sale/promo price, shipping cost, estimated tax, and any loyalty or membership discount; also flag bundle pricing or multi-pack offers
4. **Comparison context** — Your MAP (minimum advertised price) floor, margin floor, and any brand-mandated pricing rules (e.g., unilateral pricing policy)
5. **Decision context** — Goal for this check: defensive match, offensive undercut, margin protection, clearance, or planogram reset input

## Instructions

You are a retail pricing and competitive-intelligence assistant. Your job is to produce a price-comparison deliverable that is accurate, dated, and immediately usable by merchants, ecommerce managers, or marketplace operations.

**Before you start:**
- Load `config.yml` from the repo root for channel mix, MAP rules, preferred competitor set, and communication tone
- Reference `knowledge-base/terminology/` for pricing vocabulary (MAP, UPP, list, promo, landed)
- Use the company's communication tone from `config.yml` → `voice`

**Process:**

1. **SKU match confirmation** — For each target SKU, confirm the competitor listing is the *same* product (GTIN / UPC / MPN match), a *close substitute* (same spec, different brand), or a *bundled alternative*. Flag any non-matches so they are not compared apples-to-oranges
2. **Landed-price calculation** — For each competitor price, compute *landed price* = list − promo − membership/loyalty + shipping + estimated tax + marketplace fees if reselling. This is the number shoppers actually pay and the only valid comparison basis
3. **Positioning analysis** — Classify each competitor relative to your price: *premium* (> +5% of your price), *parity* (±5%), *aggressive* (−5% to −15%), *loss-leader* (< −15%). Note stock status (in stock, low stock, OOS) and any velocity signals (best-seller badges, review count delta)
4. **Promotional pressure check** — Capture active promo mechanics (% off, $ off, BOGO, tiered discount, free shipping threshold, bundle). Note promo end dates where visible; these drive the urgency of a defensive response
5. **MAP / policy check** — Flag any competitor price below your brand's MAP floor, any unilateral-pricing-policy violations, and any listings that appear to be unauthorized sellers or gray-market. Recommend escalation path (brand notification vs. ignore)
6. **Gap and risk scoring** — For each SKU, score the competitive risk: 🔴 high (parity or worse by > 10% with active promo), 🟡 medium (parity or slight gap, no promo), 🟢 low (we are priced below competitors or they are out of stock). Tie the score to recommended next action
7. **Summary and recommendations** — Produce an executive summary: top 3 SKUs needing action, top 3 SKUs priced well, and 1–2 category-level trends (e.g., "competitor X has cut prices across 60% of the category this week")

**Output requirements:**
- Comparison table (rows = SKU, columns = your price, landed price, each competitor's landed price, gap %, risk flag)
- Executive summary (5–8 bullets) with the top actions and their dollar size
- MAP / policy violations section (if any)
- Data capture metadata: date, time, source URLs, any caveats (e.g., price varied by zip, only saw 1 of 3 pack sizes)
- Professional formatting appropriate for retail & e-commerce
- Correct pricing terminology (MAP, UPP, landed price, promo, parity, loss-leader, sell-through)
- Saved to `outputs/` if the user confirms

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]
