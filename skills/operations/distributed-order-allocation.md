---
name: "Distributed Order Allocation"
category: operations
tools: [claude, chatgpt]
difficulty: advanced
time_saved: "~50 min/cycle"
version: 1.1
last_eval_score: null
---

# 📦 Distributed Order Allocation

## Purpose

Move a multi-node retail network from a static "ship from nearest DC" routing rule to an agent-orchestrated, per-order allocation decision: for every digital order line, pick the node — DC, store, dark store, vendor drop-ship, marketplace partner, or split-shipment combination — that minimizes total landed cost, respects the customer's promised delivery window, protects store labor capacity, and reflects the merchant's carbon and brand-experience targets. Output is an approval-ready allocation policy and a per-order decision packet that writes back to the order management system (OMS) without an irreversible commit until the human policy gate is cleared.

## When to Use

Use this skill when (a) a merchant is rolling out an agentic OMS toolkit (IBM Sterling Agentic, Fluent Commerce, Manhattan Active Omni, Blue Yonder, Kibo, Korber, or a custom MCP-based router on top of an existing OMS), (b) ship-from-store is consuming associate hours and creating fulfillment errors that show up as customer complaints, (c) the network is adding a new node type (dark store, locker, marketplace partner, vendor-direct drop-ship) and the routing logic needs to be re-derived rather than patched, (d) carbon and packaging-waste targets are entering the routing rule for the first time and a defensible policy is needed, or (e) cross-border duties / de-minimis changes (US Section 321 ending or tightening, EU IOSS, UK low-value threshold) are reshaping the per-order landed cost and the merchant has to re-route on a tighter cost-of-service envelope. Distinct from `inventory-reorder-brief` (when and how much to reorder into nodes), `agentic-assortment-planner` (which SKUs to allocate by store cluster), `demand-forecasting-brief` (unit demand by node), `dynamic-pricing-strategy` (price moves on the assortment in place), and `agentic-checkout-fraud-shield` (purchase-side fraud at checkout): this skill is the *post-checkout, pre-ship node-and-mode allocation* call.

## Required Input

Provide the following:

1. **Network topology** — Node list with node type (DC, dark store, store, locker, marketplace partner, vendor drop-ship), inventory-feed cadence per node, ship-from capability per node (capable, dark, paused, peak-only), pick-pack capacity per shift, cutoff times by carrier, hours of operation, and the carrier services available from each node (small parcel, freight, same-day courier, BOPIS / curbside)
2. **Inventory ledger** — Real-time on-hand and committed by SKU × node, in-transit by SKU × node × ETA, safety-stock level by node, and the freshness of each feed (timestamp + staleness threshold). Flag nodes whose feed is older than the freshness threshold so the agent does not allocate against ghost inventory
3. **Order context per line** — Customer ZIP / postal code, promised delivery window (e.g., "by Tue Apr 28"), shipping option chosen at checkout (standard, expedited, same-day), residential vs. commercial flag, gift / signature-required flag, hazmat / age-restricted / RX flags, and the channel the order came in on (own site, marketplace, ChatGPT / agent storefront, BOPIS pickup)
4. **Cost-to-serve inputs** — Carrier rate cards by zone × weight × service, expected packaging cost per node, pick-pack labor cost per node (loaded), node-to-customer transit days by zone, and the de-minimis / IOSS / VAT / duty rule that applies to each origin-destination pair if cross-border
5. **Service-level standards** — On-time-in-full target by channel, split-shipment policy (allowed, allowed-with-notice, banned), brand promise on packaging or unboxing, and the tolerance for missed promised delivery window (in % of orders, not just an absolute count)
6. **Store-labor protection rules** — Maximum store-pick share of daily fulfillment volume per store (so ship-from-store doesn't cannibalize selling-floor coverage), peak-period blackout windows, store-pick decline rules (when a store can refuse a route without penalty), and the integration with the labor scheduling system so a store flagged as understaffed in `labor-scheduling-agent` is not auto-assigned new ship-from-store volume
7. **Carbon and packaging targets** — Emissions accounting model the merchant uses (GLEC / ISO 14083 / SBTi-aligned), per-order carbon target if any, packaging-waste rules, right-size carton library, and any region where ground-only or rail is preferred over air for sustainability or cost reasons
8. **OMS write-back surface** — Which OMS the allocation writes into (IBM Sterling, Fluent, Manhattan Active Omni, Blue Yonder, Kibo, Salesforce OMS, Shopify Component, custom), whether the write is a soft assignment the OMS confirms or a hard commit, and the rollback window for re-routing if a node feed turns out stale

## Instructions

You are an always-on order-allocation co-pilot for a retail fulfillment team. Your job is to pick the right node and mode for every order without overloading a store, missing a promise, or paying for an air shipment that a ground move would have served. Never auto-allocate against a node feed that is older than the staleness threshold for that node — surface the stale-feed condition and pause routing for that node until the feed refreshes. Never recommend splitting a shipment in violation of the merchant's split-shipment policy without a single human approval gate. Never auto-route hazmat, RX, age-restricted, or signature-required lines through a node that lacks the corresponding compliance posture (state RX license, alcohol carrier, hazmat training, signature-on-delivery), even if it is the lowest-cost option.

**Before you start:**

- Load `config.yml` from the repo root for: `banner`, `node_directory`, `carrier_rates`, `service_level_targets`, `split_shipment_policy`, `store_pick_max_share`, `peak_blackout_windows`, `compliance_postures` (RX, alcohol, age, hazmat), `carbon_target`, `parity_rules`, `voice`
- Reference `knowledge-base/terminology/` for OMS and fulfillment vocabulary (DOM, ship-from-store, BOPIS, BORIS, dark store, dwell, cutoff, OTIF, last-mile, zone-skip, NDR, RTO, de-minimis, IOSS, IOR, OTW, MPK, AWB, harmonized code)
- Reference `knowledge-base/regulations/` for the de-minimis / IOSS / VAT / duty rules that apply to the merchant's lanes (US Section 321 / IEEPA changes, EU IOSS thresholds, UK low-value threshold, India high-cost retail rules)
- If `inventory-reorder-brief` and `demand-forecasting-brief` are recent, ingest them as the baseline for node-level inventory expectations; otherwise treat the inventory ledger as the only source and label the recommendation as inventory-only

**Process:**

1. **Frame the prize per channel** — Translate the cost-to-serve and service-level standards into a per-order target landed cost and a target on-time-in-full rate by channel. Express the prize as a dollar number (delta vs. last-period actual: shipping $ saved + missed-promise $ recovered + store-labor $ protected − marginal carbon offset $ paid). Flag any channel where the math says single-node DC routing already meets the standard — those channels get a lighter-touch policy and the agent's leverage is concentrated on the channels that don't.

2. **Per-line eligibility filter** — For each order line, eliminate nodes that fail a hard rule before any cost optimization: stale feed, paused-fulfillment status, missing compliance posture for hazmat / RX / alcohol / age, store-pick max-share already consumed for the day, peak blackout window active, or carrier cutoff already passed for the chosen ship method. The remaining set is the eligible-node set; if it is empty, the line is escalated to a human routing queue with the named blockers.

3. **Cost-and-carbon scoring** — For each eligible node, compute a composite score: total landed cost (carrier rate by zone × weight × service + packaging + pick-pack labor + duties / IOSS / VAT) + carbon penalty (per `carbon_target` shadow price) + store-labor protection penalty (rises as the store approaches its max-share for the day) − reuse / recommerce credit if the node is fulfilling a refurbished or trade-in unit. Sort eligible nodes by score and surface the top 3 with the cost components broken out so a reviewer can see why one beat another.

4. **Split-vs-single shipment evaluation** — If a single-node fulfillment is infeasible (no node has every line in the order on hand), evaluate split-shipment options against the merchant's policy: (a) banned — defer the order to a backorder workflow with a fresh promise window, (b) allowed-with-notice — generate the notice text and route only after the customer's notice preference is satisfied, or (c) allowed — pick the split that minimizes the sum of landed costs while keeping every parcel inside the promised window. Show the customer-experience cost (extra parcels, extra delivery dates) alongside the dollar cost in the packet.

5. **Store-labor protection pass** — Re-validate every store-pick allocation against `store_pick_max_share` and the live state of `labor-scheduling-agent` for that store. If a store is flagged understaffed for the day or the share threshold is already at 90%, route the line to the next-best non-store node and surface the trade-off (cost delta vs. selling-floor protection) on the dashboard. Never push the store past the share threshold silently.

6. **Cross-border and duty pass** — For any order crossing a duty / VAT line, re-evaluate the eligible-node set against the de-minimis / IOSS / VAT / IOR rule for that lane. If the de-minimis rule recently changed (e.g., the US Section 321 environment, EU IOSS thresholds, UK low-value threshold, India retail rules), recompute landed cost with the current rule, not the cached rate, and flag the change in the rationale so a finance reviewer can audit the call. Never auto-route a low-value international parcel through a lane that has lost de-minimis treatment without surfacing the duty cost.

7. **Carrier and mode selection** — Within the chosen node, pick the carrier service that meets the promised window at the lowest landed cost: ground if the transit-day window allows, expedited only when ground misses the promise, zone-skip if the volume warrants and the carrier supports it, regional-parcel where the lane is favorable. For same-day or next-day promises in dense urban zones, evaluate same-day courier or store-to-door against expedited parcel and pick the cheaper one that still hits the window. Surface the carrier alternative the agent did *not* pick and the reason, so the operator can spot a stale rate card.

8. **Failure-mode planning** — For each allocation, name the most likely failure mode (stale feed catches up and unit isn't actually picked, store declines the route, carrier service-fail, weather embargo, customs hold) and the agent's auto-recovery move (re-allocate to the next-best node, switch service, hold for next-cutoff, escalate). Set a hard wall on automatic re-routing — at most N retries before a human is in the loop — and write the audit trail in a way that survives a chargeback / SAFE Act / consumer complaint review.

9. **Approval-ready packet assembly** — Group allocations by exception type for the human reviewer: clean auto-allocations (no review needed), threshold-bumping store routes, split shipments under "allowed-with-notice", cross-border lines with new duty exposure, and the unrouted-line escalation list. Cap the reviewer's queue at a length they can clear in one sitting (default 50 exceptions / reviewer / cycle) and sort by absolute dollar impact + customer-experience risk.

10. **Write-back plan and rollback window** — Specify the OMS endpoint each allocation writes into, whether the write is a soft assignment (reservation that the OMS confirms) or a hard commit (the carrier label is bought), and the rollback window — at most N minutes for hard commits, longer for soft assignments. Never recommend hard-committing an irreversible cost (international air with non-refundable duty prepay, locker injection that can't be recalled) without an explicit human approval gate even if the agent's allocation policy would otherwise permit it.

11. **KPI scorecard and rollback triggers** — Define the weekly scorecard: OTIF rate by channel, split-shipment rate, store-pick share vs. ceiling, ship-from-store decline rate, average landed cost per channel, carbon emissions per order, exception clearance time, mis-route incidence, and customer NPS / complaint rate on shipping. Include rollback triggers that pause the agent (revert to rule-based routing) if mis-route rate, OTIF regression, or store-decline rate crosses a defined threshold for two consecutive cycles.

12. **Config-utilization checklist** — Confirm the packet uses `node_directory`, `carrier_rates`, `service_level_targets`, `split_shipment_policy`, `store_pick_max_share`, `peak_blackout_windows`, `compliance_postures`, and `carbon_target` from `config.yml` rather than generic placeholders. Cite the named de-minimis / IOSS / VAT / duty rule (not just "duty applies") for every cross-border line.

**Output requirements:**

- **Cycle header** — Banner / period / channel scope / inventory-feed freshness summary / OTIF target vs. landed-cost target
- **Eligibility heat-map** — Node × order-class grid with eliminated nodes marked with the named blocker (stale feed, missing posture, peak blackout)
- **Per-line allocation packet** — Grouped by exception type, capped per reviewer, each row: order, node, carrier, service, landed cost, carbon, customer-promise check, top non-picked alternative with reason
- **Split-shipment summary** — Count, dollar impact, customer-experience cost, notice-template links
- **Cross-border duty summary** — Lane × duty rule × landed-cost delta, with the named regulatory rule cited
- **Store-labor protection summary** — Per-store share consumed, decline rate, escalations
- **Failure-mode rule book** — Failure modes × auto-recovery moves × max-retry × audit-trail line
- **KPI scorecard spec with rollback triggers**
- **Write-back plan** — OMS endpoint, soft-vs-hard commit, rollback window
- **Config-utilization checklist**
- **Professional formatting** appropriate for a fulfillment-operations director and a finance partner audience
- **Correct OMS and fulfillment terminology** (DOM, OTIF, ship-from-store, BORIS, dark store, zone-skip, cutoff, NDR, RTO, de-minimis, IOSS, IOR, harmonized code, dwell, AWB)
- Saved to `outputs/` if the user confirms

## Example Output

> Worked example — one day's allocation cycle, with two order lines shown at full depth. Every figure is internally consistent and recomputable from the inputs shown; a real run substitutes the merchant's own node feeds, rate cards, and `config.yml`.

### Cycle header

**Banner:** Harborline Home (240 doors, home & hardware) · **Cycle:** 2026-07-13, digital orders 06:00–18:00 · **Volume:** 18,400 order lines
**Network:** 3 DCs · 6 dark stores · 240 ship-from-store-capable doors · 40 vendor drop-ship SKUs
**Feed freshness:** 246 of 249 nodes current. **3 nodes over staleness threshold** → **Store #031, #147, DS-5 are paused for routing this cycle** (not de-prioritized — *eliminated*, per the hard rule). Ghost-inventory exposure avoided on an estimated 84 lines.
**Targets:** OTIF 96.4% (channel: own-site standard) · landed cost/order $10.95

### Prize framing (step 1)

Annualized on 4.6M digital orders, with routing leverage on the **62%** of volume that has more than one eligible node (the other 38% is single-node by geography or SKU and gets a lighter-touch rule-based policy — the agent adds nothing there and should not be run on it):

| Component | Math | $ |
|---|---|---|
| Shipping saved | 4.6M × 62% × $0.89/order | **+$2,538,280** |
| Missed-promise recovered | 4.6M × 3.3 OTIF pts × $4.10 recovery cost avoided | **+$622,380** |
| Store labor protected | 41,000 selling-floor hrs × $19.40 loaded | **+$795,400** |
| Carbon offset paid | 4.6M × 62% × $0.06 shadow price | **−$171,120** |
| **Net prize** | | **≈ $3.78M/yr** |

Marketplace and BOPIS channels already clear the OTIF standard on single-node routing → flagged as light-touch, excluded from the prize.

### Worked line 1 — the cheapest node is not the eligible node

**Order #DG-88214** · 1 × 22" 2-stroke gas string trimmer (**hazmat: fuel system**) · Denver 80211 · promised **Thu Jul 16**, standard · residential

**Step 2 — eligibility filter:**

| Node | Hard-rule check | Eligible? |
|---|---|---|
| **DS-3 (Aurora, zone 1)** | **No hazmat compliance posture** | ❌ **ELIMINATED** |
| Store #118 (Denver, zone 1) | Hazmat-trained pickers ✓ · store-pick share 78% of ceiling ✓ · cutoff 15:20 not passed ✓ | ✓ |
| DC-West (Reno, zone 4) | Hazmat ✓ · capacity ✓ · cutoff ✓ | ✓ |
| Store #031 | **Feed stale** | ❌ ELIMINATED |

**Step 3 — cost-and-carbon scoring on the eligible set:**

| Node | Carrier | Packaging | Pick-pack | Landed | Carbon (kg × $0.12) | Store-labor penalty | **Score** |
|---|---|---|---|---|---|---|---|
| **DC-West** ✅ | $9.84 | $1.35 | $2.10 | $13.29 | 2.1 kg → $0.25 | $0.00 | **$13.54** |
| Store #118 | $6.42 | $1.35 | $4.85 | $12.62 | 0.6 kg → $0.07 | **$2.40** (78% of ceiling) | $15.09 |
| *DS-3 (ineligible)* | *$6.42* | *$1.35* | *$2.95* | *$10.72* | *$0.07* | *$0.00* | *$10.79* |

**Allocation: DC-West, ground, 3-day transit, arrives Jul 16 ✓ (promise met with 0 days of slack).**

Two things a reviewer needs to see and both are on the packet: **(i)** the *cheapest* node by raw landed cost, DS-3 at $10.79, was eliminated on a compliance posture, not on price — a router that optimizes cost first would have shipped a fuel-system product from a node with no hazmat certification; **(ii)** Store #118 is cheaper on landed cost ($12.62 vs $13.29) but loses on the composite once the store-labor penalty is priced in — at 78% of its daily ship-from-store ceiling, the marginal pick costs the selling floor more than the $0.67 of shipping it saves. **Top non-picked alternative: Store #118, +$1.55 composite, reason: store-labor protection.** If the merchant disagrees with the penalty curve, that is a `config.store_pick_max_share` conversation — the agent surfaces it rather than burying it.

### Worked line 2 — split-vs-single under "allowed-with-notice"

**Order #DG-90733** · 3 lines (12-pk LED bulbs · 4 × 40lb mulch · cordless drill) · promised **Fri Jul 17** · policy: **allowed-with-notice**

No single node holds all three lines. Options evaluated (step 4):

| Option | Nodes | Landed cost | Parcels | Delivery dates | Promise |
|---|---|---|---|---|---|
| **A ✅** | Store #204 (bulbs + drill) + DC-East (mulch) | **$29.80** ($11.20 + $18.60) | 2 | Jul 15, Jul 17 | ✓ both inside window |
| B | DC-West single, mulch backordered | $12.90 + reship | 1 (+1) | Jul 15, **Jul 22** | ❌ **misses promise** |
| C | DC-East, all three | $34.10 | 1 | Jul 17 | ✓ |

**Recommend A** — $4.30 cheaper than C and both hit the window. **The customer-experience cost is stated next to the dollar, not hidden behind it:** A means 2 parcels on 2 dates against C's single delivery. Notice text generated and queued; the line routes **only after** the customer's notice preference is satisfied. If the merchant's brand promise weights unboxing/single-delivery above $4.30, C is the right call and the packet gives the reviewer everything needed to make it in one click.

### Cross-border duty summary (step 6) — 220 lines, US → CA

| Lane | Rule applied | Cached rate | Recomputed | Δ landed |
|---|---|---|---|---|
| DC-East → ON, order CA$168 | **CUSMA de-minimis: CA$40 tax-free / CA$150 duty-free — order exceeds both** | duty-free (stale) | duty + GST/HST assessed | **+CA$14.20/order** |

The cached carrier table still carried the pre-threshold treatment. **The duty cost is surfaced on the packet before routing, with the named rule cited** — not discovered at the customer's door as an unexpected collect-on-delivery charge, which is the failure mode this pass exists to prevent. Finance can audit the call from the rationale line.

### Store-labor protection summary

| Store | Share consumed | Ceiling | Decline rate | Action |
|---|---|---|---|---|
| #118 | 78% | 85% | 4% | Penalty applied; 1 line re-routed to DC-West |
| #204 | 41% | 85% | 2% | Clean |
| #093 | **91%** | 85% | 11% | 🔴 **Over ceiling — hard-blocked from new routes today.** Cross-check: `labor-scheduling-agent` flags #093 understaffed (2 NCNS). Zero new ship-from-store volume assigned. |

### Failure-mode rule book (abridged)

| Failure mode | Auto-recovery | Max retries | Audit line |
|---|---|---|---|
| Stale feed catches up, unit not actually there | Re-allocate to next-best eligible node | **2**, then human | node, timestamp, ghost-qty, re-route target |
| Store declines route | Next-best non-store node; increment decline counter | 1 | store, reason code, decline-rate impact |
| Carrier service-fail | Switch service within promise; else notify + new promise | 1 | carrier, service, promise delta |
| Customs hold | **No auto-retry** → human queue | **0** | lane, HS code, rule cited |

Audit trail is written to survive a chargeback / consumer-complaint review: every record carries the eligible set, the eliminated nodes *with their named blockers*, the composite score, and the non-picked alternative.

### Approval-ready packet (step 9) — reviewer queue, capped at 50

| Exception class | Lines | Reviewer action |
|---|---|---|
| Clean auto-allocations | 17,932 | None |
| Threshold-bumping store routes | 214 | Batch-approve or re-route |
| Split under "allowed-with-notice" | 138 | Confirm notice sent |
| Cross-border with new duty exposure | 220 | Finance sign-off |
| **Unrouted — escalated** | **31** | 🔴 Named blockers: 22 = no eligible node w/ hazmat posture in-region · 9 = every node stale |
| Stale-feed suppressed | 84 | Ops: refresh 3 node feeds |

Sorted by |$ impact| + CX risk; **50-exception cap enforced — 553 exceptions exist, so the top 50 are surfaced and the remainder is a constraint-checked backlog, not a dropped queue.**

### KPI scorecard and rollback triggers

OTIF by channel · split-shipment rate · store-pick share vs. ceiling · ship-from-store decline rate · landed cost/order · kg CO₂e/order (GLEC) · exception clearance time · **mis-route incidence** · shipping-complaint rate.
**Rollback to rule-based routing** (agent paused) if, for **two consecutive cycles**: mis-route > 0.5%, OTIF regresses > 1.0 pt vs. baseline, or store-decline rate > 15%.

### Write-back plan

| Allocation type | OMS endpoint | Commit | Rollback window | Gate |
|---|---|---|---|---|
| Standard domestic | Manhattan Active Omni — soft reservation | Soft | Until label purchase | Auto |
| Store-pick | Manhattan + store task app | Soft | 45 min | Auto |
| **Hard commit (label bought)** | Manhattan | **Hard** | **15 min** | Auto within policy |
| **International air w/ non-refundable duty prepay** | Manhattan | **Hard, irreversible** | **None** | 🔴 **Explicit human approval gate — always.** |

### Config-utilization checklist

`node_directory` ✓ (249 nodes; 3 stale flagged) · `carrier_rates` ✓ (⚠️ CA lane table stale — flagged) · `service_level_targets` ✓ (OTIF 96.4%) · `split_shipment_policy` ✓ (allowed-with-notice) · `store_pick_max_share` ✓ (85%; #093 blocked) · `peak_blackout_windows` ✓ (none active) · `compliance_postures` ✓ (**hazmat eliminated the cheapest node**) · `carbon_target` ✓ ($0.12/kg shadow price) · `parity_rules` ✓ · `voice` ✓
**Unavailable / backfill:** no `packaging.carton_library` → right-size carton selection fell back to a flat $1.35 packaging estimate per node. Backfill to make the carbon and packaging lines real rather than nominal.

## Notes

- The agent does not own irreversible commits. Every hard-commit is gated by an explicit approval rule from `config.yml` and surfaced with the full landed-cost breakdown so the reviewer can see why a more expensive node was picked over a cheaper one (e.g., a store posture that lets the agent serve an alcohol order a DC cannot).
- Ghost inventory is the most common failure mode. Treat node-feed staleness as a hard rule, not a soft preference. A merchant who allocates against ghost inventory pays twice — once for the cancellation, once for the customer-recovery move — and the agent's job is to refuse that allocation.
- Store-labor protection is a brand-experience input, not a finance afterthought. A store at its share ceiling that gets an extra route is a store that under-staffs the selling floor at peak. The agent must reflect that cost in its score, not just the shipping line.
- Cross-border de-minimis is in motion in 2026. Treat the cached rate as suspect for any lane that has seen a regulatory change, and recompute landed cost from `knowledge-base/regulations/` rather than from a stale carrier table.
- This skill consumes outputs from `inventory-reorder-brief`, `demand-forecasting-brief`, and `labor-scheduling-agent` and writes back into the OMS — it does not replace any of them. When integrated with `agentic-assortment-planner` and `dynamic-pricing-strategy`, the allocation policy can also surface a "should we have stocked this in the first place" feedback loop to the buying team.
