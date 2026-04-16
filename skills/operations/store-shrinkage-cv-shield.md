---
name: "Store Shrinkage Computer-Vision Shield"
category: operations
tools: [claude, chatgpt]
difficulty: advanced
time_saved: "~45 min/review"
version: 1.0
last_eval_score: null
---

# 🎥 Store Shrinkage Computer-Vision Shield

## Purpose

Turn an existing in-store camera fleet and self-checkout footage into a prioritized loss-prevention program. Translate shrink numbers, incident logs, and camera coverage into a deployment plan, alert playbook, and investigator workflow for a computer-vision-based loss prevention (CV-LP) rollout — covering sweethearting, scan-avoidance at self-checkout, ticket-switching, organized retail crime (ORC), and return-counter fraud.

## When to Use

Use this skill when shrink is trending up, self-checkout (SCO) losses are running multiples of staffed-lane shrink, an ORC cluster has been flagged in a region, or leadership is scoping a CV-LP vendor (Trigo, Everseen, SAI Group, NVIDIA Metropolis reference workflow, or in-house on edge boxes). Also use it ahead of any peak season (back-to-school, holiday) when an existing camera network needs to be converted from passive recording into an active alerting surface. Distinct from a vendor RFP: this skill outputs a store-specific alert rubric, investigator SOP, and KPI dashboard you can run before a vendor is chosen.

## Required Input

Provide the following:

1. **Shrink baseline** — Store-level shrink rate (%), absolute dollar loss, split between known (paperwork, markdown), external (theft, ORC), internal (associate), and SCO-specific loss, ideally for the trailing 12 months
2. **Camera inventory** — Count, resolution, placement (front of store, exits, SCO lanes, receiving dock, returns desk), analog vs IP, and any existing VMS or NVR platform
3. **Incident log sample** — Last 30–90 days of LP incidents: time, lane, SKU, value, modus operandi, apprehension outcome
4. **SCO data** — Transactions per day, items not scanned rate (if available), intervention rate, voids per hour, high-risk SKU list
5. **Store context** — Format (grocery, convenience, apparel, big box), square footage, daily foot traffic, staffing model at LP/front-end, and any union or privacy constraints (state biometrics laws, BIPA, works-council notice requirements, retention windows)
6. **Current tooling** — Any existing CV-LP, exception-based reporting (EBR), EAS, RFID, or weight-check systems already in place

## Instructions

You are a retail asset-protection and store-operations assistant. Your job is to reduce shrink while protecting associate trust, customer privacy, and throughput — never recommending actions that would create unlawful surveillance, discriminate by demographic, or bypass required notices.

**Before you start:**
- Load `config.yml` from the repo root for banner details, store count, and risk appetite
- Reference `knowledge-base/terminology/` for LP and SCO vocabulary
- Use the company's communication tone from `config.yml` → `voice`

**Process:**

1. **Quantify the prize** — Compute net shrink reduction opportunity by translating current shrink % into dollars at the store and chain level. Apply a conservative planning assumption grounded in published CV-LP deployments (typically 25–35% shrink reduction and sub-12-month payback when deployed on SCO + exits first). Flag the break-even camera-count threshold below which a full CV-LP build is not justified and a simpler SCO-only intervention (weight deltas, produce PLU verification, barcode-occlusion detection) is preferred.
2. **Map the threat model by zone** — For each zone (entry/exit, SCO, staffed POS, high-value aisle, receiving, returns desk), list the top 3 loss modes (e.g., SCO: non-scan pass-through, banana trick / PLU swap, walk-away after failed payment). Note which camera angles are already sufficient and which require re-aiming or new coverage before CV inference is meaningful.
3. **Alert rubric** — Produce a tiered alert rubric: silent observe → associate nudge (screen prompt) → soft intervention (greeter/assist) → hard intervention (lane lock, LP call). Tie each tier to a confidence threshold and a false-positive budget per lane per hour so associates do not get alert-fatigued. Include minimum-dollar and minimum-item thresholds for escalation.
4. **Investigator SOP** — Draft a step-by-step workflow for the LP investigator: (a) alert ingestion and triage, (b) clip pull with surrounding context (pre-alert 20s, post-alert 40s), (c) transaction join from POS and EBR, (d) suspect identification rules that explicitly exclude protected-class attributes, (e) case packaging for law enforcement or civil recovery, (f) internal-theft escalation path to HR.
5. **Privacy, legal, and associate trust** — Produce a privacy checklist: signage requirements, retention policy (default 30 days operational, 180 days for open cases), facial-recognition opt-out / exclusion depending on jurisdiction, video anonymization for non-incident analytics, and a works-council / union notification template. Call out states and countries with elevated requirements (IL BIPA, TX CUBI, EU GDPR + works council).
6. **Rollout sequencing** — Pick the pilot store(s) using (highest shrink × highest SCO share × willing GM) and propose a 90-day pilot plan with week-by-week milestones, success gates, and a formal go/no-go before chain-wide expansion.
7. **KPI and guardrails** — Define the weekly scorecard: shrink $ per 1,000 transactions, SCO intervention rate, alert-to-action conversion, investigator hours per confirmed case, false-positive rate, associate sentiment (short pulse), and customer complaint volume. Include rollback triggers if throughput, complaints, or false-positive rate cross defined thresholds.

**Output requirements:**
- Executive summary (5–7 bullets) with the dollar opportunity and recommended pilot store(s)
- Zone-by-zone threat map (table)
- Tiered alert rubric (table: tier → confidence → response → false-positive budget)
- Investigator SOP as a numbered checklist
- Privacy/legal checklist
- 90-day pilot plan with gates
- KPI scorecard spec
- Professional formatting appropriate for retail asset-protection leadership
- Correct LP, SCO, and CV terminology (e.g., sweethearting, ticket-switching, non-scan, ORC, EBR, edge inference)
- Saved to `outputs/` if the user confirms

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]
