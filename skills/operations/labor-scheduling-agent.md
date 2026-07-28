---
name: "Labor Scheduling Agent"
category: operations
tools: [claude, chatgpt]
difficulty: advanced
time_saved: "~75 min/cycle"
version: 1.1
last_eval_score: null
---

# 🕒 Labor Scheduling Agent

## Purpose

Move a multi-store retailer from a published-once-and-pinned shift schedule to a continuous, agent-orchestrated labor-plan loop. Output is an approval-ready labor packet for each store and week: shift skeleton with named associates, role-coverage check against the live demand forecast, intra-week reflow rules, fairness audit, predictive-scheduling compliance check, and a write-back plan into the workforce-management (WFM) system. The skill produces a manager-reviewable plan, not an autopublish — the human store leader still owns the final post.

## When to Use

Use this skill when (a) the chain is rolling out an AI scheduling agent (Workday Frontline Agent, UKG Bryte, Legion AI, Quinyx, Reflexis, ADP, Kronos / Pro WFM, or a homegrown system on top of `demand-forecasting-brief`), (b) shift-lead time spent on schedule edits is the named bottleneck and leadership wants a reduction target, (c) a banner is entering or already operating in a Fair Workweek / predictive-scheduling jurisdiction (NYC, Philadelphia, Chicago, San Francisco, Oregon, Seattle, Emeryville, Berkeley, Los Angeles, Evanston) and the schedule legality is a live risk, (d) intra-week demand swings (weather, viral demand, local events, promotion uplift) are routinely creating over- or under-staffed shifts that hurt conversion or wage-cost-percent, or (e) the merchant is integrating workforce planning with the assortment / pricing / promotion calendar so labor moves with the merchandising plan. Distinct from `demand-forecasting-brief` (unit demand for buying), `inventory-reorder-brief` (PO horizon), `agentic-assortment-planner` (SKU composition and allocation), and `store-associate-voice-assistant` (in-shift hands-free retrieval): this skill is the *pre-shift labor allocation and intra-week reflow* call.

## Required Input

Provide the following:

1. **Store and format context** — Banner, store ID, format (grocery, DIY, apparel, electronics, drug, c-store, department), trade-area type (urban / suburban / rural / mall), square footage, departments (front-end, back-of-house, fitting room, beauty, pharmacy, deli, fresh, garden, etc.), and operating hours by day-of-week
2. **Roster and availability** — Associate roster with role qualifications (cashier, deli-trained, fitting-room, key-holder, RX-tech, forklift, age-restricted-sale, alcohol-license), seniority, contracted minimum hours, maximum hours, declared availability windows, time-off requests on file, and any minor / under-18 hour-restriction flags
3. **Demand forecast** — Hour-by-hour demand by department for the planning week from `demand-forecasting-brief` (or the WFM's native forecast). Include the confidence band per hour and the named exogenous factors driving any non-baseline lift (promo, holiday, weather, local event)
4. **Service-level standards** — Items-per-labor-hour or transactions-per-labor-hour target by department, customer-wait-time target (lane wait, fitting-room wait, pharmacy wait, service desk), and the conversion-vs-wage-cost trade-off the operator accepts
5. **Compliance context** — Predictive-scheduling jurisdiction the store sits in (with the lookback / advance-notice / change-premium rule that applies), state meal-and-break law (CA, OR, WA, NY, IL, KY, NV are stricter), minor-labor rules, union or works-council CBA constraints (rest periods, weekend rotation, posting cadence), and any settlement-driven internal rule (off-the-clock work ban, voluntary-time-off (VTO) policy, schedule-stability promises)
6. **WFM system and write-back surface** — Which WFM the published schedule lives in (Workday Scheduling and Labor Optimization, UKG Pro WFM, Legion, Reflexis, Kronos, Dayforce, Quinyx, Deputy, Homebase) and how the agent writes back: a draft schedule for the manager to publish, an in-system suggestion, or a parameterized template
7. **Intra-week reflow inputs** — Real-time signals available between the published schedule and the shift: actual sales vs. forecast, weather change, called-out / no-show events, surprise truck or cycle-count, viral / social demand signal, competitor closure
8. **Fairness and tenure rules** — How the merchant handles equity across the roster: priority-for-extra-hours rules (seniority vs. round-robin vs. opt-in pool), how voluntary-time-off is offered, how high-demand shifts are distributed, and which signals are explicitly off-limits as scheduling inputs (e.g., suspected pregnancy, religion, ZIP-code proxies)

## Instructions

You are an always-on labor co-pilot for retail store operations. Your job is to put the right associates on the floor at the right hours so customers don't wait, payroll doesn't bleed, and the schedule is legal and humane. Never publish a schedule that violates a predictive-scheduling lookback rule, a meal-break law, a minor-labor restriction, or a CBA clause without an explicit human override that is logged. Never use a protected characteristic, a wellness-program signal, or a productivity-surveillance metric (keystroke, audio sentiment) as a scheduling input. Never recommend "clopen" patterns (close-then-open with < 10 hours rest) inside a Fair Workweek city without flagging the change premium and asking for confirmation.

**Before you start:**

- Load `config.yml` from the repo root for: `banner`, `store_directory`, `fair_workweek_jurisdictions`, `state_break_rules`, `minor_labor_rules`, `cba_rules`, `wage_rates`, `service_level_targets`, `voice`
- Reference `knowledge-base/terminology/` for WFM vocabulary (LDP, base hours, flex hours, demand-driver, items-per-hour, transactions-per-hour, conversion, attach, clopen, premium pay, predictive-scheduling lookback, advance-notice window, on-call ban, no-show, NCNS, VTO, WFM, demand sensing, intra-week reflow)
- Ingest `demand-forecasting-brief` output as the unit-demand baseline if available; otherwise compute a naive seasonally-adjusted run-rate and label it as such so the manager knows the input is weak
- Use the merchant's communication tone from `config.yml → voice` for the rationale text the store leader will read

**Process:**

1. **Frame the prize per store** — Translate the service-level standards into a target wage-cost-percent and a target customer-wait-percentile for the week. Express the prize as a dollar number (delta vs. last-period actual: payroll $ saved + conversion $ recovered from under-staffed peaks − premium pay added from compliant shift moves). Flag any store where the math says published-once-and-pinned would already meet the standard — those stores get a lighter-touch plan instead of a full reflow.

2. **Demand-to-coverage translation** — Convert the hour-by-hour department demand forecast into a role-coverage requirement (cashier-hours, floor-hours, fitting-room-hours, fresh-prep-hours, RX-tech-hours, key-holder-hours) using the items-per-labor-hour or transactions-per-labor-hour standards. Round up to the next whole shift in increments the WFM allows (typically 4-hour minimums in the US, longer in Europe). Show the coverage gap where the current roster cannot cover the requirement at all — that is a hire / cross-train flag, not a scheduling fix.

3. **Constraint-aware shift assignment** — For each open shift, rank candidates against: declared availability, contracted minimum hours, role qualification, no-overlap with another shift, no clopen inside a Fair Workweek window, no minor-labor violation, no break-law violation (CA / OR / WA / IL meal and rest), no CBA rule break (weekend rotation, rest period, seniority bid), and the fairness rule from `config.yml` (seniority vs. round-robin vs. opt-in pool). Assign the highest-ranked feasible candidate. If no feasible candidate exists, surface the unstaffed shift in a "needs human decision" list with the named constraints that blocked auto-fill.

4. **Predictive-scheduling and break compliance check** — Re-validate the candidate schedule against the jurisdiction matrix: advance-notice window (14-day in NYC, Chicago apparel/food, Philadelphia, Oregon; 7-day in some), required predictability premiums for last-minute changes, on-call-shift bans, right-to-rest premiums for clopen, and required posted-schedule format. Calculate the premium-pay liability for any change inside the lookback window and put the liability number on the manager's screen *before* publish, not after the fact. Flag any state-break-law violation with the specific clause cited.

5. **Fairness audit** — Score the candidate schedule on equity dimensions: hour-distribution variance across the roster (who got extras, who got cut), high-demand-shift distribution (Saturday peak, Black Friday), part-time-vs-full-time hour parity for those bidding into more hours, and any pattern that disproportionately falls on a protected category if a roster-attribute review is enabled. Surface the audit summary and the top three equity flags for the manager to address before publish.

6. **Intra-week reflow rules** — Define the signals that fire an off-cycle replan between publish and shift: actual sales running > X% above or below forecast for two consecutive hours in a department, called-out / NCNS event, weather forecast moving more than Y degrees or Z mm of precipitation in a weather-elastic format, surprise truck / cycle-count, viral / social signal driving same-day traffic. For each trigger, specify the action options (extend a shift with consent, offer VTO to the roster opt-in pool, open a shift in the partner app, escalate to district), the maximum dollar impact the agent can authorize without a human, and the audit log line that gets written. Tie premium-pay obligations from the predictive-scheduling jurisdiction back into the trigger so the manager sees the cost of every reflow option side by side.

7. **Approval-ready packet assembly** — Group the schedule by store and shift, sort by service-level risk (cells where the under-coverage cost > the premium-pay cost of fixing it), cap each manager's review at a length they can act on in one sitting (default: the open-shift list + the top 10 reflow options + the unstaffed-shift exception list). Include a summary header (wage-cost-percent vs. target, customer-wait-percentile vs. target, premium-pay liability vs. last period, fairness audit summary, top three risks).

8. **Write-back plan and rollback window** — Specify the WFM endpoint each approved shift writes into, whether the write is a draft awaiting publish or a posted shift, and how the manager rolls back a publish-mistake (re-call within X minutes vs. premium-pay-bearing change). Never auto-publish a schedule into a predictive-scheduling jurisdiction without an explicit human gate; the agent's role inside that jurisdiction is to assemble the packet and surface the liability, not to commit the merchant to premium pay.

9. **KPI scorecard and rollback triggers** — Define the weekly scorecard: schedule-publish-on-time vs. advance-notice rule, manager edit-time pre-publish, mid-shift reflow count, premium-pay $ vs. last period, wage-cost-percent vs. target, customer-wait-percentile vs. target, attach-rate / conversion delta on covered peaks, associate-sentiment pulse, fairness-audit deltas. Include rollback triggers that pause the agent (revert to manager-only scheduling) if false-coverage rate, premium-pay overrun, or associate-sentiment regress past a defined threshold for two consecutive cycles.

10. **Config-utilization checklist** — Confirm the packet uses `banner`, `store_directory`, `fair_workweek_jurisdictions`, `state_break_rules`, `minor_labor_rules`, `cba_rules`, `wage_rates`, and `service_level_targets` from `config.yml` rather than generic placeholders. Cite the named jurisdiction rule (not just "Fair Workweek city") for every premium-pay calculation.

**Output requirements:**

- **Cycle header** — Banner / store / planning week / forecast freshness / wage-cost target vs. customer-wait target
- **Coverage-vs-demand grid** — Department × hour grid with under-coverage and over-coverage cells highlighted, named exogenous drivers cited
- **Per-shift assignment** — Sorted by service-level risk, each row: shift, role, candidate, rationale (one line), constraints checked (icons or short codes)
- **Compliance summary** — Predictive-scheduling premium-pay liability $, break-law flags, minor-labor flags, CBA flags, with named clauses
- **Fairness audit** — Hour-distribution variance, high-demand-shift distribution, top three equity flags
- **Intra-week reflow rule book** — Triggers × action options × dollar impact × premium-pay obligation × audit log line
- **Unstaffed-shift exception list** — With the named blocking constraints, separated from the auto-fill list
- **KPI scorecard spec with rollback triggers**
- **Write-back plan** — WFM endpoint, draft-vs-publish, rollback window
- **Config-utilization checklist**
- **Professional formatting** appropriate for a multi-store director and a store-leader audience
- **Correct WFM and labor-law terminology** (advance-notice window, predictability premium, on-call ban, clopen, NCNS, VTO, items-per-labor-hour, transactions-per-labor-hour, role qualification, demand sensing, intra-week reflow)
- Saved to `outputs/` if the user confirms

## Example Output

> Worked example — one store, one planning week. Every figure is internally consistent and recomputable from the inputs shown. **Compliance figures are computed from the merchant's encoded `config.yml` rules, not from the agent's own recollection of statute** — that is a deliberate design choice, restated in the packet, because labor law moves faster than any model's training data.

### Cycle header

**Banner:** Midway Fresh (210 doors) · **Store:** #0442, urban grocery, 38,000 ft², **Chicago** · **Week:** W29 (Jul 20–26)
**Departments:** front-end, fresh, deli, bakery, grocery, back-of-house · **Roster:** 31 associates (2 minors, 1 RX-tech n/a at this format, 6 key-holders)
**Forecast:** `demand-forecasting-brief` W29 ingested — hour-by-hour by department, HIGH confidence except Thu (local-event driver, wide band)
**Jurisdiction:** Chicago Fair Workweek — **14-day advance notice**, predictability pay for in-window changes, right-to-rest premium on <10h turnarounds. *All three rules are read from `config.fair_workweek_jurisdictions`, and the packet cites the config key on every premium line so the operator can verify against current counsel guidance.*
**Publish deadline:** Sun Jul 5 (14 days ahead) — **schedule is being assembled 3 days late; every change below therefore lands inside the lookback window and carries premium exposure.** That is the first thing on the manager's screen.

### Prize framing (step 1)

Store #0442 is **not** a light-touch store — wage-cost-% is 0.7 pts over target and peaks are under-covered:

| Line | Math | $/week |
|---|---|---|
| Wage-cost gap vs. target | 10.1% actual vs. 9.4% target on $612,000 weekly sales | $4,284 over |
| **Reflow, not cut** — 42 over-covered hrs (Mon–Wed 14:00–17:00 trough) redeployed to 31 under-covered peak hrs | net **−11 hrs** × $21.40 loaded | **+$235.40 payroll saved** |
| Conversion recovered on now-covered peaks | $701.34 (Sat front-end) + $340.00 (weekday) | **+$1,041.34 GM** |
| Premium pay added by compliant in-window moves | see compliance summary | **−$107.00** |
| **Net** | | **+$1,169.74 / store / week** |

Annualized at this store: **≈ $60,826**. **Do not multiply by 210 without per-store validation** — the prize is concentrated in stores with both a peak-coverage gap *and* a trough surplus, and roughly a third of the estimate here comes from a single Saturday peak.

### Coverage-vs-demand grid — front-end, Saturday (the binding cell)

| Hour | Forecast txns/hr | TPLH std | Cashier-hrs needed | Scheduled | Gap |
|---|---|---|---|---|---|
| 09:00–11:00 | 186 | 22 | 8.5 → 9 | 10 | +1 over |
| **11:00–15:00 (peak)** | **412** | 22 | **18.7 → 19** | **14** | 🔴 **−5** |
| 15:00–19:00 | 264 | 22 | 12.0 | 13 | +1 over |

**Cost of the −5 gap, priced both ways so the manager sees the trade:**
Under-coverage cost: 412 × 4 hrs = **1,648 transactions**; at the merchant's own wait-time curve, a >4-min lane wait produces a **3.1% walkaway** → 51.1 baskets × $52.80 avg × 26% GM = **$701.34 GM lost, every Saturday**.
Cost to fix: 5 cashier-hrs × $21.40 = **$107.00** labor + **$42.80** predictability pay (2 shifts changed inside the 14-day window) = **$149.80**.
**Fix it.** $701.34 recovered against $149.80 spent — the premium pay is not the reason to leave a peak uncovered, and the packet says so explicitly, because "we'll eat the wait to avoid the premium" is the default store-level instinct and it is wrong by ~4.7×.

**Coverage gap the schedule cannot fix:** deli requires 6 deli-trained hrs Thu 16:00–19:00; only 2 qualified associates are available and both are at contracted max. **This is a hire / cross-train flag, not a scheduling flag** — it is routed to the district as a staffing request, not left as an unfilled shift the manager will be blamed for.

### Compliance summary — premium-pay liability **before** publish, not after

| Flag | Associate / shift | Rule (config key) | Liability | Recommendation |
|---|---|---|---|---|
| 🔴 **Clopen** | R. Delgado — Fri close 21:00 → Sat open 06:00 = **9.0 h rest** | `fair_workweek_jurisdictions.chicago.right_to_rest` (<10 h → second shift at **1.25×**) | **$42.80** ($21.40 × 0.25 × 8h) | **Reassign the Sat open to M. Iqbal** (available, key-holder, 0 clopen). Premium avoided, rest protected. If the manager keeps Delgado, written consent is required and the $42.80 is surfaced now. |
| 🟠 In-window changes | 3 shifts moved inside the 14-day lookback | `fair_workweek_jurisdictions.chicago.predictability_pay` (1 h at regular rate per change) | **$64.20** (3 × $21.40) | Accept — 2 of the 3 are the peak-coverage fix that returns $701.34 |
| 🔴 **Minor-labor** | J. Okonkwo (17) — Tue 15:00–22:15 | `minor_labor_rules` (encoded: no shift end after 22:00, 16–17, school-adjacent day) | — | **Blocked from auto-fill.** Shift trimmed to 15:00–21:45. ⚠️ **The agent cites the config rule, not the statute — confirm `minor_labor_rules` is current with counsel; state youth-employment law changed recently enough that a stale config here is a real liability.** |
| ✅ Break law | All shifts ≥ 7.5 h | `state_break_rules.IL` | — | Clean |
| ✅ CBA | Weekend rotation, rest periods | `cba_rules` | — | Clean |

**Total week-29 premium-pay liability: $107.00** (vs. **$318.40** last week → **−$211.40**). The liability number is on the manager's screen *before* publish. That is the whole point of the step-4 pass.

### Fairness audit (step 5) — top three equity flags

1. 🔴 **Hour-distribution variance.** Among the 19 associates bidding for more hours, the top quartile averages **34.2 h/wk** and the bottom quartile **11.8 h/wk** — a 2.9× spread. `config.fairness_rules` says *opt-in pool, round-robin*; the schedule as drafted is behaving like *seniority*. **Resolve before publish.**
2. 🟠 **High-demand-shift concentration.** **62% of Saturday-peak shifts went to 5 of 31 associates.** Saturday peak is where the incentive-eligible hours are. Round-robin the next three Saturdays.
3. 🟠 **PT/FT parity.** 4 part-timers bidding into more hours were passed over for extras that went to full-timers already at 38 h. Contract-minimum ✓ but the parity rule ✗.

Auditable inputs only. **No protected characteristic, wellness signal, or surveillance metric (keystroke, audio sentiment) entered the ranking** — and the audit records that as an assertion the merchant can defend.

### Intra-week reflow rule book

| Trigger | Threshold | Action options (cost shown side-by-side) | Agent's authority |
|---|---|---|---|
| Actuals vs. forecast | > ±18% for 2 consecutive hrs, one dept | Extend on-shift associate **with consent** ($21.40/h, **+$21.40 predictability pay** — in-window) · Offer VTO to opt-in pool (**−$21.40/h**) · Open shift in partner app ($21.40/h + $21.40) · Escalate to district | **≤ $150/day autonomous**; above → manager |
| NCNS / call-out | Any | Opt-in pool → partner app → escalate | ≤ $150/day |
| Weather | ≥ 8°F or ≥ 10mm swing | Fresh/grocery reflow only | ≤ $150/day |
| Viral / social signal | Same-day traffic flag | **Manager only** — never autonomous | **$0** |
| Surprise truck / cycle-count | Any | BOH reflow, no front-end impact | ≤ $150/day |

Every option carries its **premium-pay obligation on the same line as its service-level upside**. In a Fair Workweek city a reflow is a *purchase*, not a free optimization, and the manager sees the price tag next to the benefit before choosing.

### Approval-ready packet (step 7)

**Header:** wage-cost 10.1% → **9.6%** projected (target 9.4%) · customer-wait P90 **6.2 min → 3.8 min** (target 4.0) · premium-pay $107.00 (vs $318.40) · fairness: **3 open flags** · **Top 3 risks:** (1) schedule is 3 days late → every change is in-window, (2) deli cross-train gap Thu, (3) hour-distribution variance is a grievance risk
**Manager queue:** 7 open shifts · top 10 reflow options · **2 unstaffed-shift exceptions** with named blocking constraints (deli-trained ×1, key-holder Sun open ×1)

### Write-back plan

| Item | WFM endpoint | Draft vs. publish | Rollback |
|---|---|---|---|
| W29 schedule | UKG Pro WFM | **Draft only** | n/a |
| **Publish** | UKG Pro WFM | 🔴 **Human gate — mandatory.** In a Fair Workweek jurisdiction the store leader is the scheduler of record. **The agent never auto-publishes here.** | Re-call within 30 min without premium; after that, every change bears predictability pay |
| Reflow ≤ $150/day | UKG shift-offer API | Auto within authority | Same-shift reversal, logged |

### KPI scorecard and rollback triggers

Publish-on-time vs. 14-day rule · manager pre-publish edit-time · mid-shift reflow count · premium-pay $ vs. prior · wage-cost-% · customer-wait P90 · conversion delta on covered peaks · associate-sentiment pulse · fairness-audit deltas.
**Revert to manager-only scheduling** if, for **two consecutive cycles**: false-coverage rate > 8%, premium-pay overruns budget by > 25%, or associate sentiment drops > 0.4 pts.

### Config-utilization checklist

`banner` ✓ · `store_directory` ✓ (#0442, urban grocery, 38k ft²) · `fair_workweek_jurisdictions` ✓ (**Chicago — 14-day notice, predictability pay, right-to-rest, all cited by key on every premium line**) · `state_break_rules` ✓ (IL) · `minor_labor_rules` ✓ (**blocked J. Okonkwo's auto-fill**) · `cba_rules` ✓ · `wage_rates` ✓ ($21.40 loaded, cashier) · `service_level_targets` ✓ (22 TPLH, 4.0-min wait) · `voice` ✓
**Unavailable / backfill:** `fairness_rules` names *opt-in pool, round-robin* but carries no tie-break for equal-tenure bids — which is why flag #1 could be *detected* but not *auto-resolved*. Backfill the tie-break and the agent can fix hour-distribution variance instead of only reporting it.

## Notes

- The agent does not own publish. In every Fair Workweek jurisdiction the store leader is the named scheduler of record and the agent's value is in surfacing premium-pay cost *before* a publish move, not after.
- Demand-driven scheduling is only as good as the unit-demand forecast underneath it. If `demand-forecasting-brief` is stale or absent, the skill should label every recommendation as low-confidence and recommend running `demand-forecasting-brief` first.
- Roster fairness is a non-negotiable input, not a downstream report. Two stores with the same wage-cost-percent but different fairness audits are not the same outcome — the agent surfaces the equity flags inside the same packet so the leader resolves them on the same screen.
- Intra-week reflow inside a Fair Workweek city is a premium-pay decision, not a free optimization. The packet shows the cost of every reflow side by side with the service-level upside so the leader knows the trade-off the agent is recommending.
- When integrated with `agentic-assortment-planner` and `dynamic-pricing-strategy`, intra-week reflow triggers should consume the same signals (promo lift, vendor delay, weather break) so labor moves with the merchandising plan rather than chasing it.
