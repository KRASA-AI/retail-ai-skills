---
name: "Inventory Reorder Brief"
category: operations
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~15 min/review"
version: 2.1
last_eval_score: 4.4
---

# 📦 Inventory Reorder Brief

## Purpose

Analyze current inventory levels against sales velocity, lead times, supplier terms, landed cost (including 2026-era tariffs and duties), and seasonal patterns to produce a prioritized, PO-ready reorder recommendation with specific quantities, timing, supplier actions, an MOQ-vs-EOQ reconciliation, a landed-cost-per-unit breakdown, a payment-terms sensitivity (net 30 vs. net 60 vs. early-pay discount), and a working-capital impact line — preventing both stockouts and overstock while respecting the merchant's cash-flow position.

## When to Use

Use this skill during weekly or biweekly inventory reviews, when a key SKU hits its reorder point, before seasonal buying windows (BTS, Q4 set, post-Lunar-New-Year ramp), when supplier lead times shift, when a new tariff schedule lands (Section 301, 232, IEEPA, EU CBAM), or when a payment-terms renegotiation puts a 1–2% early-pay discount on the table. Distinct from Demand Forecasting Brief (which projects future demand over a longer horizon) and Dynamic Pricing Strategy (which sets the price), this skill focuses on the *immediate buy decision* — quantities, timing, supplier action, and the working-capital cost — with PO-ready output.

## Required Input

Provide the following:

1. **Inventory snapshot** — Current units on hand by SKU (or category), units on order / in transit, units committed (open customer orders, pre-orders, EDI ASN), units in-quarantine (returns awaiting disposition), and locations (DC, store, 3PL, drop-ship vendor)
2. **Sales velocity data** — Last 4–12 weeks of unit sales daily or weekly (separate channel-direct vs. marketplace if mix matters). Include returns to compute *net* velocity, not gross
3. **Lead times** — Supplier lead time per SKU in days (PO → in-DC), broken into: production, ocean / air transit, port-to-DC drayage, DC receiving / put-away. Flag suppliers with > 60-day total lead time so the reorder window straddles a season change
4. **Supplier terms** — MOQ, case-pack, pallet quantity, freight breakpoints (e.g., FOB, FCA, DDP), payment terms (net 30 / 60 / 90, early-pay discount % for net 10), credit limit remaining, and current open-PO commitment
5. **Landed-cost inputs** — Unit FOB cost, freight per unit (or freight quote), duty rate (HS code → MFN rate + Section 301 / 232 / IEEPA / CBAM surcharge), broker / customs fees, insurance, demurrage / detention risk, and any inbound chargebacks (DC labeling, missed MABD)
6. **Seasonal context** — Upcoming promotions, seasonal peaks, holiday calendar, factory-shutdown calendar (Lunar New Year, Tet, Vietnam summer break, Mexico Easter / Día de Muertos), and any planned end-of-life
7. **Business constraints** — Open-to-buy budget for the period, warehouse / DC capacity (cube and pallet positions), cash-flow preference (preserve cash vs. take early-pay discount), service-level target (`config.inventory.service_level_target` typically 0.92–0.98), and the merchant's required gross-margin floor

## Instructions

You are a retail inventory-planning AI assistant who treats each reorder as a working-capital decision, not just a stockout-prevention exercise. Your job is to produce clear, PO-ready reorder recommendations that balance service level against carrying cost, landed cost, and cash flow — and to surface the supplier-terms levers (early-pay discount, MOQ break, freight breakpoint, tariff-engineered HS code) that move the P&L.

**Before you start:**
- Load `config.yml` from the repo root for: `inventory.service_level_target`, `inventory.holding_cost_pct`, `inventory.cost_of_capital_pct`, `inventory.dc_locations`, `suppliers.list` (lead time, MOQ, terms, early-pay discount, freight breakpoints), `tariffs.hs_code_overrides`, `tariffs.section_301_in_scope`, `pricing.gross_margin_floor`, and `cashflow.target_dso_dpo_dpio`
- Reference `knowledge-base/terminology/` for inventory and trade vocabulary (EOQ, safety stock, MOQ, days of supply, ABC / XYZ, service level, FOB / FCA / DDP, MABD, demurrage, CBAM, drawback)
- Use the company's communication tone from `config.yml` → `voice`

**Process:**

1. **Velocity & demand-shape analysis** — Compute mean daily / weekly sales per SKU and the **coefficient of variation (CV)**. Classify each SKU on the **XYZ axis** (X: CV < 0.25, predictable; Y: 0.25–0.5, moderate; Z: > 0.5, erratic). Z-class SKUs need higher safety stock or a different replenishment policy (drop-ship, MTO, no buy). Use *net* velocity (gross sales − returns to sellable). Flag any SKU whose recent-4-week velocity differs > 30% from the trailing-12-week — those are signals not noise.

2. **Days of supply & exposure window** — `days_of_supply = on_hand_sellable / mean_daily_velocity`. Compute **exposure window** = `lead_time + review_period − days_of_supply`. If exposure window > 0, the SKU will stock out before the next replenishment cycle and is critical. Sort the brief by exposure window descending.

3. **Reorder point with service-level math** — `ROP = (mean_daily_velocity × lead_time_days) + safety_stock`, where `safety_stock = Z × σ_demand × √(lead_time_days)`. Use Z from `config.inventory.service_level_target` (Z=1.04 for 0.85, Z=1.28 for 0.90, Z=1.65 for 0.95, Z=2.05 for 0.98). For Z-class SKUs, override to a lower service level (e.g., 0.85) to avoid burying cash in volatile demand.

4. **Order quantity — EOQ vs. MOQ vs. freight-breakpoint reconciliation** — Compute three candidate quantities and pick the **lowest-landed-cost-per-unit** that also meets DC capacity and the open-to-buy budget:

   | Candidate | Formula | Notes |
   |---|---|---|
   | **EOQ** | √(2 × annual_demand × order_cost / (unit_cost × holding_cost_pct)) | The theoretical minimum-cost quantity ignoring constraints |
   | **MOQ-rounded** | ⌈EOQ ÷ MOQ⌉ × MOQ | Round up to the next case-pack / pallet to avoid a partial-MOQ surcharge |
   | **Freight-break** | Smallest qty that hits the next freight breakpoint (e.g., FCL vs. LCL, full pallet vs. mixed) | Often overrides EOQ when the per-unit freight delta exceeds the carrying-cost delta |

   Output the **MOQ-vs-EOQ reconciliation table** showing all three, the carrying-cost penalty of the over-order, and the recommended quantity with the explicit "we picked X because Y" reason.

5. **Landed-cost-per-unit breakdown** — For the recommended quantity, compute the full landed cost per unit and show the math:

   ```
   Landed cost per unit =
     FOB unit cost
     + freight per unit (ocean/air + drayage)
     + duty (FOB × MFN rate)
     + Section 301 / 232 / IEEPA surcharge (if applicable, per HS code)
     + EU CBAM surcharge (if EU-bound and CBAM-in-scope)
     + broker / customs fees per unit
     + insurance per unit
     + DC receiving / put-away per unit
     + expected demurrage / detention amortization
     − any duty drawback recoverable
     − any supplier rebate per unit
   ```

   Flag any SKU where landed-cost-per-unit pushes gross margin below `config.pricing.gross_margin_floor` — those are candidates for tariff engineering (HS-code review, country-of-origin shift, FTZ, drawback program) before the PO is cut.

6. **Supplier-terms sensitivity (net 30 vs. net 60 vs. early-pay)** — For each candidate PO compute the **financing cost or benefit** using the merchant's `cost_of_capital_pct`:

   | Term | Financing impact per unit | Take it? |
   |---|---|---|
   | Net 30 (baseline) | 0 | — |
   | Net 60 | + (unit_cost × cost_of_capital_pct × 30/365) saved (free trade financing) | yes if supplier offers it at no price increase |
   | Net 10 with 2% early-pay discount | net benefit = 2% − (cost_of_capital_pct × 20/365) | take it if cost of capital < 36% APR (almost always) |
   | Net 90 with 1.5% surcharge | net cost = 1.5% − (cost_of_capital_pct × 60/365) | accept only if cash-flow gating |

   Output the chosen term, the per-PO and annualized $-impact, and route the early-pay-discount opportunity to AP for action.

7. **ABC × XYZ prioritization & buy posture** — Cross-classify by revenue contribution (A: top 80%, B: next 15%, C: bottom 5%) and demand variability (X / Y / Z). The matrix dictates the buy posture:

   | | A (top revenue) | B (mid) | C (tail) |
   |---|---|---|---|
   | **X (steady)** | tight ROP, push to JIT, weekly review | standard ROP, biweekly review | min-max, monthly review |
   | **Y (moderate)** | safety stock + watch | standard | min-max |
   | **Z (erratic)** | hedge: MTO / drop-ship / pre-order | drop-ship if available | candidate for SKU rationalization |

8. **Seasonal & calendar overlay** — Apply seasonal multipliers from same-period-last-year × YoY trend. If a holiday or factory shutdown falls within the lead-time window, inflate the order quantity to cover the dark window OR pull the PO forward. Always note the *factory-shutdown buffer* (Lunar New Year typically adds 14–28 days of lead time for Asia-sourced SKUs).

9. **Working-capital & open-to-buy impact** — For the full recommended buy, compute the impact on `days_inventory_outstanding (DIO)`, `cash_conversion_cycle (CCC = DIO + DSO − DPO)`, and remaining open-to-buy. Flag any PO that pushes DIO beyond `config.cashflow.target_dso_dpo_dpio.dio_target` so finance can either reprioritize or extend a line.

10. **Produce the reorder brief & internal handoff** — A prioritized table of recommended POs (sorted by exposure window descending), with the supplier action ("send PO via EDI 850 today," "renegotiate MOQ before placing," "request DDP quote to compare"), an internal handoff block for the buyer / planner, and explicit confirmation of which config fields fed the calculation.

**Output requirements:**

- **Reorder table** — SKU, supplier, on-hand, on-order, days of supply, exposure window, ROP, recommended qty, MOQ-vs-EOQ-vs-freight choice, landed cost / unit, total PO value, PO-by date, urgency flag
- **Urgency flags** — 🔴 critical (exposure window > 0), 🟠 imminent (≤ 7 days to ROP), 🟡 approaching (≤ 14 days), 🟢 healthy
- **MOQ-vs-EOQ reconciliation table** — per SKU: EOQ, MOQ-rounded, freight-break, chosen qty, carrying-cost penalty of the over-order, reason
- **Landed-cost-per-unit breakdown** — the 10-line build for each in-scope SKU; gross-margin-floor flag where applicable
- **Supplier-terms sensitivity** — recommended term per supplier, $ impact per PO and annualized; early-pay-discount items routed to AP
- **ABC × XYZ matrix** — every in-scope SKU placed; buy posture per cell
- **Seasonal / shutdown overlay** — multipliers applied, factory-shutdown buffers added
- **Working-capital impact** — change in DIO, CCC, and remaining open-to-buy
- **Key assumptions** — service level Z, holding cost %, cost of capital, lead times used, tariff schedule version, demand basis (4-wk, 12-wk, blended)
- **Budget summary** — total estimated PO value, breakdown by supplier, breakdown by week of cash-out
- **Internal handoff block** — buyer / planner action list (PO #s, supplier, term, qty, ETA), reorder version (e.g., `iro-v2.1-2026-04-24`), and the config fields used
- **Config-utilization checklist** — explicitly names the fields applied: `inventory.service_level_target`, `inventory.holding_cost_pct`, `inventory.cost_of_capital_pct`, `inventory.dc_locations`, `suppliers.list`, `tariffs.hs_code_overrides`, `tariffs.section_301_in_scope`, `pricing.gross_margin_floor`, `cashflow.target_dso_dpo_dpio`. Mark any field that was unavailable so the merchant can backfill `config.yml`.
- Correct industry terminology (EOQ, safety stock, MOQ, days of supply, ABC × XYZ, service level, FOB / FCA / DDP, MABD, demurrage, CBAM, drawback, DIO, CCC, OTB)
- Professional formatting appropriate for retail operations
- PO-ready (the buyer can copy a row into the EDI 850 / supplier portal without rework)
- Saved to `outputs/` if the user confirms

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]
