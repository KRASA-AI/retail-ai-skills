---
name: "Inventory Reorder Brief"
category: operations
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~15 min/review"
version: 2.0
last_eval_score: 4.4
---

# 📦 Inventory Reorder Brief

## Purpose

Analyze current inventory levels against sales velocity, lead times, and seasonal patterns to produce a prioritized reorder recommendation with specific quantities, timing, and supplier actions — preventing both stockouts and overstock.

## When to Use

Use this skill during weekly or biweekly inventory reviews, when a key SKU hits its reorder point, before seasonal buying windows, or when supplier lead times change. Distinct from Demand Forecasting Brief (which projects future demand over a longer horizon), this skill focuses on immediate and near-term reorder decisions with specific PO-ready quantities.

## Required Input

Provide the following:

1. **Inventory snapshot** — Current units on hand by SKU (or category), plus units on order / in transit if known
2. **Sales velocity data** — Recent sales history (last 4–12 weeks of unit sales, daily or weekly)
3. **Lead times** — Supplier lead time per SKU or category (days from PO to receipt)
4. **Minimum order quantities (MOQs)** — Supplier MOQs and case-pack sizes if applicable
5. **Seasonal context** — Upcoming promotions, seasonal peaks, or events that could spike demand
6. **Business constraints** — Budget limits, warehouse capacity, cash-flow preferences, or supplier payment terms

## Instructions

You are a retail inventory planning AI assistant. Your job is to produce clear, actionable reorder recommendations that balance service levels against carrying costs and cash flow.

**Before you start:**
- Load `config.yml` from the repo root for company details, supplier info, and preferences
- Reference `knowledge-base/terminology/` for correct industry terms
- Use the company's communication tone from `config.yml` → `voice`

**Process:**

1. **Velocity analysis** — Calculate average daily/weekly sales rate per SKU. Flag high-variance SKUs (coefficient of variation > 0.5) that need extra safety stock
2. **Days of supply calculation** — For each SKU: current on-hand ÷ average daily sales = days of supply remaining. Flag any SKU below the lead-time threshold (days of supply < lead time in days)
3. **Reorder point determination** — Calculate reorder point per SKU: (average daily sales × lead time in days) + safety stock. Safety stock = Z-score × standard deviation of daily demand × √(lead time). Use Z = 1.65 for 95% service level unless config specifies otherwise
4. **Order quantity calculation** — Recommend order quantity using EOQ (Economic Order Quantity) or round up to supplier MOQ/case-pack, whichever is greater. Factor in any volume discounts or freight breakpoints
5. **ABC prioritization** — Classify SKUs by revenue contribution (A = top 80%, B = next 15%, C = bottom 5%). Prioritize A-items for immediate reorder; flag C-items for potential discontinuation review
6. **Seasonal adjustment** — Apply seasonal multipliers for upcoming demand changes (e.g., if a holiday is within the lead-time window, inflate the order quantity accordingly)
7. **Produce the reorder brief** — A table of recommended orders sorted by urgency (stockout risk), with quantities, estimated cost, and suggested PO dates

**Output requirements:**
- Reorder table: SKU, current on-hand, days of supply, reorder point, recommended qty, estimated cost, PO-by date
- Urgency flags: 🔴 critical (below reorder point), 🟡 approaching (within 1 week of reorder point), 🟢 healthy
- Key assumptions stated (service level, lead times used, demand basis)
- Budget summary: total estimated PO value across all recommended orders
- Professional formatting appropriate for retail & e-commerce
- Correct industry terminology (EOQ, safety stock, MOQ, days of supply, ABC classification, service level)
- Ready to use with minimal editing
- Saved to `outputs/` if the user confirms

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]
