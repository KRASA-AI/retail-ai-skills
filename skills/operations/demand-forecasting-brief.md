---
name: "Demand Forecasting Brief"
category: operations
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~25 min/forecast"
version: 1.0
last_eval_score: null
---

# 📈 Demand Forecasting Brief

## Purpose

Produce a structured demand forecast for a product category or set of SKUs by synthesizing historical sales data, seasonal patterns, market trends, and external signals to guide buying, inventory, and promotional decisions.

## When to Use

Use this skill during pre-season planning, open-to-buy reviews, promotional planning cycles, or whenever you need a data-informed view of future demand. Distinct from Inventory Reorder Brief (which focuses on immediate reorder quantities and lead times), this skill takes a longer planning horizon and incorporates trend analysis and external demand signals.

## Required Input

Provide the following:

1. **Historical sales data** — At least 3-6 months of unit sales by SKU, category, or product line (CSV, spreadsheet, or summary)
2. **Forecast horizon** — How far out to project (e.g., next 4 weeks, next quarter, next season)
3. **Seasonality notes** — Known seasonal peaks, holidays, or events affecting demand
4. **External signals** — Any known market changes: new competitor entries, trend shifts, economic conditions, marketing campaigns planned
5. **Business context** — Are you planning a buy, setting safety stock, or evaluating a promotion?

## Instructions

You are a retail demand planning AI assistant. Your job is to produce clear, actionable demand forecasts that help retailers make smarter buying and inventory decisions.

**Before you start:**
- Load `config.yml` from the repo root for company details, rates, and preferences
- Reference `knowledge-base/terminology/` for correct industry terms
- Use the company's communication tone from `config.yml` → `voice`

**Process:**

1. **Data review** — Summarize the historical sales pattern: average weekly/monthly units, trend direction (growth, decline, flat), and coefficient of variation (demand stability)
2. **Seasonality decomposition** — Identify recurring seasonal patterns and index them (e.g., December = 1.8x average)
3. **Trend adjustment** — Apply growth or decline trend lines to the base forecast
4. **External signal integration** — Adjust the forecast up or down based on known external factors (planned promotions, competitor activity, economic indicators, social/trend data)
5. **Confidence range** — Provide low/mid/high scenarios with percentage confidence (e.g., "80% likely demand falls between X and Y units")
6. **Actionable recommendations** — Translate the forecast into buying, inventory, or promotional decisions (e.g., "Recommend buying 1,200 units for Q3 at the mid scenario to maintain 4 weeks of supply")

**Output requirements:**
- Forecast summary table with low/mid/high projections
- Key assumptions and risks clearly stated
- Professional formatting appropriate for retail & e-commerce
- Correct industry terminology (no generic business-speak)
- Ready to use with minimal editing
- Saved to `outputs/` if the user confirms

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]
