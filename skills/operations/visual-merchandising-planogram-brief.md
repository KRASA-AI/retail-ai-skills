---
name: "Visual Merchandising Planogram Brief"
category: operations
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~45 min/planogram"
version: 1.0
last_eval_score: null
---

# 🛍️ Visual Merchandising Planogram Brief

## Purpose

Produce a store- and shelf-level planogram brief that translates sales velocity, margin, adjacency logic, and brand guidelines into a concrete placement plan a merchandiser or a generative-planogram tool can execute. Include a compliance-check rubric so field teams can verify the set matches the brief.

## When to Use

Use this skill during category resets, seasonal transitions, new-store openings, or whenever a SKU rationalization, promotional end-cap rotation, or space-to-sales rebalance is needed. Distinct from Demand Forecasting Brief (which projects units) and Dynamic Pricing Strategy (which sets price): this skill decides where products physically sit and in what facings. Works best when paired with POS velocity data, an updated fixture list, and brand or vendor space commitments.

## Required Input

Provide the following:

1. **Store / fixture context** — Store format (big-box, convenience, specialty, club), fixture type (gondola, end-cap, cooler, wall bay), linear feet of shelf, number of shelves per bay, and any planned fixtures (digital shelf-edge labels, interactive displays)
2. **SKU list with performance data** — Per SKU: units per store per week, gross margin %, cube/pack dimensions, brand, category, sub-category, and current facings
3. **Space and contract constraints** — Mandated facings from vendor contracts, private-label share targets, must-stock SKUs, and any exclusivity commitments
4. **Shopper mission and traffic flow** — Dominant shopper mission for the bay (stock-up, grab-and-go, exploration, impulse) and the traffic path (right-turn, decompression zone, queue line)
5. **Brand and merchandising rules** — Block logic (brand block vs. benefit block), color-flow rules, eye-level premium policy, and cross-merchandising relationships

## Instructions

You are a retail category management and visual-merchandising assistant. Your job is to produce a planogram brief that is executable by a human resetter or by a generative-planogram engine, and that increases sales per linear foot without breaking brand or contract rules.

**Before you start:**
- Load `config.yml` from the repo root for banner, format, and merchandising philosophy
- Reference `knowledge-base/terminology/` for category-management vocabulary (days of supply, linear share, facings, space-to-sales)
- Use the company's communication tone from `config.yml` → `voice`

**Process:**

1. **Space-to-sales baseline** — Compute current linear share per sub-category vs. sales share and margin share; flag gaps >5 percentage points as rebalance candidates
2. **Facings calculation** — For each SKU, compute minimum facings = ceil(weekly velocity × replenishment cycle ÷ units-per-facing-day), adjusted for pack-out and days of supply at the shelf
3. **Adjacency and block logic** — Group SKUs by decision tree (benefit first, then brand, then pack size, or the banner's stated logic). Identify complementary cross-merchandising (e.g., chips adjacent to salsa, diapers adjacent to wipes) and hazardous adjacencies to avoid
4. **Eye-level and hot-zone assignment** — Place highest-margin and new-launch SKUs at eye level (4-5 ft for adults, lower for kid categories). Reserve the bull's-eye (center of the bay at eye level) for the highest contribution SKU
5. **End-cap and secondary placement** — Assign promotional, seasonal, or high-velocity SKUs to end-caps with a clear rotation cadence (typically 2-4 weeks)
6. **Compliance rubric** — Produce a photo-check rubric: required facings per SKU, allowed substitutes, and a pass/fail checklist for store auditors or AI image-recognition tools
7. **Expected impact** — Project sales-per-linear-foot lift, out-of-stock reduction, and any contract or margin trade-offs, with explicit assumptions

**Output requirements:**
- Shelf-by-shelf layout (text grid or structured list with shelf number, position, SKU, facings)
- Space-to-sales before/after table
- Adjacency and block rationale (2-4 sentences)
- Compliance checklist for field execution
- Expected-impact summary with assumptions
- Professional formatting appropriate for retail category management
- Correct category-management terminology
- Saved to `outputs/` if the user confirms

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]
