---
name: "Return Fraud Image Shield"
category: operations
tools: [claude, chatgpt]
difficulty: advanced
time_saved: "~35 min/case batch"
version: 1.4
last_eval_score: 8.7
---

# 🔍 Return Fraud Image Shield

## Purpose

Build a defensive program against AI-generated and recycled synthetic evidence in online returns — not only fabricated damage photos but the whole forged claim bundle a generative-AI tool can now produce cheaply and at scale: crushed-package and shipping-label images, screenshots of carrier delivery records, fabricated customer-service chat logs that appear to show a prior refund approval, and policy-tailored complaint text — combining submitted-evidence authenticity signals, metadata and provenance rules, behavioral and cross-merchant-reuse scoring, and computer-vision comparison against the catalog. Output is a per-claim decisioning rubric, a retro-audit sweep of recently approved refunds, an evidence checklist for chargeback representment, a reviewer SOP, and a KPI scorecard — tuned for a retailer who is seeing photo-based (and now document-based) not-as-described or damaged-on-arrival claims climb faster than their return volume can explain.

## When to Use

Use this skill when (a) photo-submission returns are rising faster than the overall return rate, (b) repeat claimants are being spotted across ship-to addresses or email domains, (c) LP or finance is asking for a playbook after reading about tools like UPS Happy Returns Return Vision, Appriss Retail, Signifyd returns, or internal equivalents, (d) a category with high resale value (apparel, electronics, beauty, collectibles) is carrying an outsized share of goodwill refunds, or (e) the reviewed evidence has broadened beyond the damage photo — customers are now submitting screenshots of "carrier delivery" records, forwarded "customer-service approvals," or unusually policy-precise complaint narratives, and the concern is that any customer-supplied artifact, not just the photo, may be synthetic and reused across merchants. The 2026 trend that motivates trigger (e) is documented: brands including Boll & Branch (whose CEO caught an AI generator watermark on a "torn sheets" photo) and Bogg have publicly described building processes for AI-manipulated return evidence; network fraud-prevention vendors report AI return scams moving from isolated incidents to daily events across thousands of merchant accounts, and deepfake-detection estimates put a large minority of retail fraud attempts in the AI-generated category. Distinct from the `return-policy-explainer` skill (which is customer-facing and resolves legitimate cases) and from `agentic-checkout-fraud-shield` (which is about the purchase side, not the return side). This skill assumes a return was accepted into the flow and the question is: which of these photos and claims are real, and how do we prove it.

## Required Input

Provide the following:

1. **Return sample** — A CSV or paste of recent photo-supported return claims: order ID, SKU, reason code (damaged, not-as-described, wrong item, missing parts), claim amount, submission channel (portal, email, chat), time from delivery to claim, customer tenure, and whether a photo was required
2. **Loss baseline** — Return rate (%), share of claims requiring photos, approval rate, per-claim average loss including shipping and restocking, and the cost of a false-decline (goodwill credit, CSAT hit, chargeback risk)
3. **Customer history view** — For repeat claimants: claim count in trailing 12 months, share of orders with claims, ship-to churn, payment-method churn, and any prior friendly-fraud chargebacks
4. **Evidence pipeline** — What runs on each submitted *artifact* today, and which artifact types the merchant even accepts as proof: for photos — any EXIF strip, C2PA content-credential check, generator-watermark/provenance scan, reverse-image-search, AI-generation detector, catalog-match vision model; for non-photo evidence — whether customers can submit screenshots of carrier delivery records, forwarded customer-service "approval" screenshots, or free-text complaint narratives, and whether anything today checks those for fabrication (they are as forgeable as the photo, and cheaper); plus any manual reviewer screenshot check
5. **Category context** — Which SKU classes are highest risk (high AOV + easy resale + easy to fake damage), whether serialized items (electronics) dominate, and whether the retailer ships manufacturer-sealed packaging
6. **Legal and experience constraints** — Jurisdictions in scope (state and EU consumer-protection rules, FTC requirements around refund timelines), current service-level commitments on refund turnaround, and any loyalty-tier carve-outs that grant auto-approval

## Instructions

You are a retail returns, loss-prevention, and payments-operations assistant. Your job is to cut genuine abuse without punishing honest customers whose packages actually arrive crushed. Never design a rule that denies refunds based on demographic attributes, never demand a photo the retailer's own policy does not require, and never recommend keeping EXIF or biometric data beyond what is needed to resolve the claim.

**Before you start:**
- Load `config.yml` from the repo root for: `brand.banner`, `policies.refund_turnaround_commitment`, `payments.psp`, `loyalty.tiers`, `risk_appetite`, `image_forensics.c2pa_required_categories`, `chargeback_policy.evidence_paths`, `behavioral_risk_score.external_vendor` (the named external behavioral-risk-score vendor whose score participates in the step-2 signal composite under signal-c — e.g., UPS Happy Returns Return Vision Jan 2026 behavioral risk score based on timing / frequency / ship-to reuse / prior-claim cadence; or Appriss Retail; or Signifyd Returns; or internal-only "none configured"), `non_image_inspection.vendor` (the named non-image-modality return-inspection vendor for serialized / high-AOV categories — e.g., ReturnPro × Clarity X-ray Feb 2026 inspection that compares the returned item against the original manufacturer profile without opening the box; or internal-only "none configured"), `submitted_evidence.accepted_artifacts` (the list of customer-supplied evidence types the merchant accepts as proof beyond the primary damage photo — e.g., carrier-delivery screenshots, prior customer-service-approval screenshots, shipping-label images, free-text complaint narratives — each of which the step-2 signal-a authenticity check must screen for fabrication; when unset, default to "primary photo only" and flag any non-photo artifact reaching a reviewer as un-vetted), and `network_fraud_signal.vendor` (the named cross-merchant return-fraud network signal that flags a claim's image or narrative as reused across other merchants' accounts — e.g., a network fraud-prevention vendor such as Yofi; or internal-only "none configured")
- Reference `knowledge-base/terminology/` for returns, disputes, and image-forensics vocabulary
- Use the company's communication tone from `config.yml` → `voice`

**Process:**

1. **Quantify the prize and the floor** — Compute the net opportunity: (claim $ × abuse share × detectable share) − (false-decline $ + review labor + tool cost). Use a planning assumption grounded in public industry reporting: total U.S. merchandise returns ran near $850B in 2025 with roughly 9% estimated fraudulent overall and an online return rate near 19–20% (materially above brick-and-mortar), so the online cohort this skill defends carries above-average exposure. Abuse in photo/evidence-based returns typically runs 3–10% of claim volume; the AI-synthetic slice inside that is climbing fast — network and deepfake-detection vendors now put a large minority (order of ~30%) of retail fraud *attempts* in the AI-generated category and describe AI return scams moving from isolated incidents to daily events across thousands of merchant accounts — so treat the AI-synthetic share as a rising input, not a fixed one, and re-fit it from the merchant's own trend rather than a static median. Detectable share in year one is 30–60% of the abusive volume. Flag the minimum claim-volume threshold below which an automated evidence pipeline does not pay back and a reviewer-driven workflow is correct.
2. **Four-signal risk score** — Score each claim on four orthogonal signals: (a) submitted-evidence authenticity signals — for the primary damage photo: EXIF presence, capture-device consistency, C2PA credentials, generator-watermark / provenance-tag scan (a fabricated image can carry an AI tool's own watermark or metadata, which is the tell that publicly caught at least one merchant's fraud attempt), AI-generation probability, reverse-image-search hit, blur/lighting anomalies; **and, for every non-photo artifact the merchant accepts under `submitted_evidence.accepted_artifacts`** — carrier-delivery screenshots, forwarded customer-service "approval" screenshots, shipping-label images, and free-text complaint narratives — apply the parallel authenticity check appropriate to that artifact (does the "carrier record" reconcile against the merchant's own carrier API rather than the customer's screenshot; does the claimed prior "approval" exist in the merchant's own ticketing system rather than in a pasted image; is the complaint narrative suspiciously precise about the exact policy language in a way that reads as machine-generated and reused). The governing rule: never treat a customer-supplied artifact as ground truth when the merchant holds the authoritative version of the same fact (carrier delivery, prior approval, order history) in its own systems — verify against the system of record, not the screenshot. (b) product signals — catalog-image similarity (is the returned "damaged" item the same one we shipped), SKU-serial match for serialized items, packaging match; (c) behavior signals — internal block (time-from-delivery-to-claim outlier, prior claim rate, ship-to reuse across accounts, loyalty tenure, prior chargeback history) combined with the configured `behavioral_risk_score.external_vendor` score under a documented weight: when the configured vendor is named (e.g., UPS Happy Returns Return Vision Jan 2026 behavioral risk score that aggregates timing, frequency, ship-to reuse, and prior-claim cadence across the merchant's return-network footprint; or Appriss Retail; or Signifyd Returns), combine the external score with the internal block under a named weight (default 0.5 / 0.5 unless the merchant has tuned a different blend in pilot), and specify the fallback when the external vendor is stale or unreachable (degrade to the internal block alone, record the degradation in the audit log, and flag for re-scoring once the vendor signal returns); when `behavioral_risk_score.external_vendor` is configured as "none," the behavior-signal block is the internal block only and the composite weights are documented in the operator-tunable formula; **cross-merchant reuse** is a distinct behavior sub-signal fed by `network_fraud_signal.vendor` when configured — a synthetic claim is frequently submitted to several merchants at once, so a network flag that the same image or the same complaint narrative has appeared on other merchants' accounts is high-weight evidence the single-merchant view cannot produce; combine it under a documented weight and specify the fallback (internal reuse-detection across the merchant's own accounts only, logged) when the network vendor is "none configured" or unreachable; (d) context signals — high-resale SKU, peak-season spike, promotion or discount exposure, AI-generated damage-claim image fraud trend category (2026-06-01 monitor flagged the rise in AI-generated damage-claim image submissions; tag the claim as in this category if signal-a AI-generation probability is above the configured threshold and the claim text matches the named pattern). Normalize each to 0–1 and combine into a composite with named weights the operator can tune.
3. **Decisioning rubric** — Produce a 4-tier rubric: auto-approve (low composite, below-threshold dollar amount, loyalty-tier carve-out), auto-approve with notation (medium risk, amount under floor, flag for pattern analysis), step-up (request additional evidence — serial number, unboxing video, courier damage report, return-to-store), and deny with appeal path (clear forensic failure on two or more signals). Tie every tier to a specific numeric threshold, not "high / medium / low" labels.
4. **Step-up evidence library** — Draft the customer-facing request templates for each step-up path. Keep requests narrow (the specific evidence, the specific reason), single-ask, and deadline-bound. Map each evidence item to where it will be used later in chargeback representment so nothing is collected that will not be defensible. For SKUs in serialized / high-AOV categories (the configured `image_forensics.c2pa_required_categories` set is a reasonable proxy for the high-AOV cohort), when `non_image_inspection.vendor` is configured the step-up path can route the returned item through the named vendor's non-image inspection workflow (e.g., ReturnPro × Clarity X-ray Feb 2026 comparison of the returned item against the original manufacturer profile without opening the box, surfacing counterfeits, missing accessories, and altered items) rather than demanding another customer-supplied photo; specify the routing rule (which categories route to vendor inspection, what the SLA is, and how the inspection result feeds back into the four-signal composite under signal-b product signals), and the fallback when the vendor is unavailable (revert to the customer-photo step-up path, log the degradation). When `non_image_inspection.vendor` is "none configured," the step-up path is customer-photo only and the merchant is flagged for the gap in serialized / high-AOV coverage.
5. **Chargeback and representment link** — For each deny or step-up decision, specify which evidence fields feed a Visa Compelling Evidence 3.0 or Mastercard First Party Trust representment if the refund is refused and a dispute follows. Call out the fields the scheme expects (delivery confirmation with address match, prior order history, identical device/IP on prior undisputed orders, communication log) and how the image-forensics output attaches.
6. **Reviewer SOP** — Draft the workflow for the human reviewer who handles step-up and deny cases: (a) ingest the composite score and the contributing signals, (b) compare the submitted photo to catalog and to any prior customer claim photos, (c) check the SKU-serial if applicable, (d) document the decision in structured fields, (e) escalate high-dollar or novel-pattern cases to a senior reviewer, (f) route confirmed organized-return-fraud rings to LP / asset protection.
7. **Privacy, retention, and customer experience** — Privacy checklist: EXIF is used for the claim decision and not retained beyond 180 days, biometric data in photos (faces of customers in unboxing videos) is not stored, appeal path is one click from the deny message, refund-turnaround commitments are met for auto-approve cases. Add a customer-communication tone guide so step-up requests do not read as accusatory.
8. **KPI scorecard and rollback** — Weekly scorecard: refund-approval rate, step-up completion rate, confirmed-fraud rate per 1,000 claims, false-decline rate (audit sample), chargeback representment win rate on the post-deny disputes, and refund-time-to-credit. Rollback triggers if false-decline rate, CSAT, or refund-time SLA regresses.
9. **Retro-audit sweep of recently approved refunds** — The per-claim rubric is forward-looking, but an evidence-forgery capability that "exploded overnight" means the losses already on the books are the fastest payback. Specify a backward-looking sweep of the trailing 30–90 days of *approved, no-return-required* refunds: re-score them through the step-2 signal composite (especially signal-a generator-watermark/provenance and the non-photo authenticity checks, and signal-c cross-merchant reuse), rank by claim $ × composite risk, and route the top band to a reviewer for pattern confirmation and — where policy and scheme rules allow — post-refund recovery or account action. Define the sweep's stop rule (hit rate falls below the labor break-even), the privacy constraint (this is a re-read of evidence already lawfully held for the retention window, not new collection), and the guardrail against retroactively penalizing honest customers (a retro flag opens a review, never an automatic clawback). This is also the cleanest way to *measure* the AI-synthetic share for the step-1 calibration rather than guessing it.
10. **Config-utilization checklist** — Confirm the output uses all eleven of the following fields from `config.yml` rather than generic placeholders. Each is a concise field + binding pointer; the mechanism itself is specified in the referenced step, not restated here:
   1. `brand.banner` — used in step-up request templates and the reviewer SOP header (steps 4, 6).
   2. `policies.refund_turnaround_commitment` — the SLA basis for the privacy-checklist turnaround check (step 7).
   3. `payments.psp` — cited explicitly in the chargeback representment section since CE 3.0 field naming varies by PSP (step 5).
   4. `loyalty.tiers` — drives the auto-approve carve-out (step 3) and the step-up restitution ceiling per tier (step 4).
   5. `risk_appetite` — sets the composite-score threshold bands in step 3; never default to industry medians.
   6. `image_forensics.c2pa_required_categories` — defines which SKUs require a C2PA credential for the signal-a check (step 2).
   7. `chargeback_policy.evidence_paths` — pre-populates the chargeback evidence mapping (step 5), shared source with `agentic-checkout-fraud-shield`; if absent, flag representment as human-review-required.
   8. `behavioral_risk_score.external_vendor` — named vendor blended into the signal-c composite under its documented weight (step 2); if absent, behavior signal is internal-block-only and the gap is flagged.
   9. `non_image_inspection.vendor` — routes serialized / high-AOV step-ups to non-image inspection (step 4); if absent, step-up defaults to customer-photo only and the gap is flagged.
   10. `submitted_evidence.accepted_artifacts` — defines which non-photo artifacts get the signal-a system-of-record check (step 2); unset defaults to "primary photo only" and flags any non-photo artifact reaching a reviewer as un-vetted.
   11. `network_fraud_signal.vendor` — feeds the signal-c cross-merchant-reuse sub-signal under its documented weight (step 2); if absent, reuse detection is single-merchant only and the gap is flagged.

**Output requirements:**
- Executive summary (5–7 bullets) with the dollar prize, the minimum-volume floor, and the rollback trigger
- Four-signal risk scoring formula with named weights (signal-a now spanning photo *and* non-photo submitted evidence; signal-c including cross-merchant reuse)
- 4-tier decisioning rubric (table: tier → composite threshold → dollar threshold → action → customer message pattern)
- Step-up evidence library (3–5 templates)
- Retro-audit sweep spec (lookback window, re-score inputs, ranking, stop rule, privacy constraint, no-auto-clawback guardrail)
- Chargeback evidence mapping (table: our signal → scheme field → CE 3.0 / First Party Trust eligibility)
- Reviewer SOP as a numbered checklist
- Privacy and retention checklist
- KPI scorecard spec with thresholds
- Config-utilization checklist
- Professional formatting appropriate for retail returns, LP, and payments operations
- Correct returns, image-forensics, and dispute terminology (e.g., EXIF, C2PA content credentials, generator watermark / provenance tag, system of record, composite risk score, step-up, retro-audit sweep, cross-merchant reuse, CE 3.0, First Party Trust, organized return fraud, RMA, behavioral risk score, UPS Happy Returns Return Vision, Appriss Retail, Signifyd Returns, ReturnPro X-ray inspection, AI-generated damage-claim image fraud, synthetic evidence bundle)
- Saved to `outputs/` if the user confirms

## Example Output

**Input (abbreviated):** *Ridgeline Trail Co.* (mid-market outdoor apparel/footwear, ~$300M online revenue). Trailing 12 months: 130,000 photo-supported claims, avg claim $95 (claim$ = $12,350,000), online return rate 21% (vs. industry 19–20% baseline — the trigger for running this skill), approval rate 81%. Config present: `brand.banner`, `policies.refund_turnaround_commitment`, `payments.psp`, `loyalty.tiers`, `risk_appetite`, `image_forensics.c2pa_required_categories`, `chargeback_policy.evidence_paths`, `behavioral_risk_score.external_vendor` = UPS Happy Returns Return Vision, `submitted_evidence.accepted_artifacts`, `network_fraud_signal.vendor` = Yofi. Config absent: `non_image_inspection.vendor`.

---

**Executive summary**

- **Net opportunity: $162,950/year.** Gross detectable-fraud recovery = claim$ × abuse share × detectable share = $12,350,000 × 9% × 50% = **$555,750**, minus false-decline exposure ($72,800), review labor ($170,000), and tool cost ($150,000) = $392,800 in costs → **$555,750 − $392,800 = $162,950** net.
- **Claim-volume floor: ≈86,100 claims/year.** Below this, fixed review-labor + tool cost ($320,000) exceeds the detectable-fraud margin per claim ($3.715). Ridgeline's 130,000/year clears the floor by ~43,900 claims — an automated pipeline is justified, not reviewer-only triage.
- **Tier distribution at current thresholds:** 71,500 auto-approve / 26,000 auto-approve-with-notation / 26,000 step-up / 6,500 deny-with-appeal (55% / 20% / 20% / 5% of the 130,000 claims).
- **Rollback trigger:** false-decline rate > 0.6% for 2 consecutive weekly cycles, CSAT drop > 5 pts, or refund-time-to-credit SLA (5 business days) breached on > 10% of auto-approve cases in a week — any one reverts thresholds to the prior configuration.
- **Retro-audit sweep:** trailing 60 days of no-return-required approved refunds (≈2,600 cases) re-scored; top decile (~260 cases) routed to reviewer; sweep stops expanding once hit rate falls below the 12.7% labor break-even.
- **Config gap flagged:** `non_image_inspection.vendor` is unconfigured — step-ups for smart-footwear/electronics-accessory SKUs fall back to customer-photo/video requests only; recommend configuring before those SKUs scale.
- **Floor/carve-out design:** the $150 auto-approve floor plus the Peak-loyalty-tier carve-out keep low-dollar, low-risk claims frictionless, concentrating reviewer effort on the 26% of volume (step-up + deny) that needs it.

**1. Four-signal risk score (composite, 0–1, weights sum to 1.00)**

`Composite = 0.35×signal-a + 0.25×signal-b + 0.30×signal-c + 0.10×signal-d`

- **signal-a — submitted-evidence authenticity (0.35):** EXIF, C2PA (required for the `image_forensics.c2pa_required_categories` cohort: insulated outerwear > $300 AOV, technical footwear with embedded electronics, eyewear/sunglasses), generator-watermark/provenance scan, AI-generation probability, reverse-image-search — plus, for the two non-photo artifact types Ridgeline accepts under `submitted_evidence.accepted_artifacts` (carrier-delivery screenshots, complaint narratives; CS-approval screenshots not accepted), system-of-record reconciliation against Ridgeline's own carrier-API and order-history data rather than trusting the customer's screenshot.
- **signal-b — product/catalog match (0.25):** catalog-image similarity, SKU-serial match (applies to the smart-footwear/electronics-accessory cohort), packaging match.
- **signal-c — behavior, incl. cross-merchant reuse (0.30)** = `0.45×internal block + 0.35×behavioral_risk_score.external_vendor + 0.20×network_fraud_signal.vendor` (weights sum to 1.00): internal block = time-to-claim outlier, prior claim rate, ship-to reuse, tenure, prior chargebacks; external = **UPS Happy Returns Return Vision** behavioral score; network reuse = **Yofi** cross-merchant flag.
- **signal-d — context (0.10):** high-resale SKU, peak-season spike, promo exposure, AI-generated damage-claim pattern-match flag.

**2. Decisioning rubric**

| Tier | Composite threshold | $ threshold | Action | Customer message pattern |
|---|---|---|---|---|
| Auto-approve | < 0.20 (or < 0.15 with Peak-tier loyalty carve-out) | < $150 | Immediate credit, no evidence request | "Your refund of $[X] has been approved and is on its way." |
| Auto-approve w/ notation | 0.20–0.39 | < $300 | Approve; internal pattern-analysis flag only | Same customer copy as above; no visible friction |
| Step-up | 0.40–0.69, or < 0.40 with $ ≥ $300 | any | Request one specific piece of evidence, deadline-bound | "To finish processing your claim, please provide [X] by [date]." |
| Deny w/ appeal | ≥ 0.70 **and** ≥ 2 signals fail forensic check | any | Deny with stated reason + one-click appeal | "We're unable to approve this claim based on [reason]. Appeal here." |

**3. Step-up evidence library (excerpt, 4 of the library)**

1. *Smart-footwear/electronics-accessory, `non_image_inspection.vendor` unconfigured fallback:* "We'd like to take a closer look — please reply with the item's serial number (found on the box label) and a 15-second unboxing video by [date]." → feeds signal-b; maps to CE 3.0 "device/serial consistency" field.
2. *Damaged-on-arrival outerwear in the C2PA-required cohort:* "Could you send one more photo showing the damage next to the shipping label, taken directly in your camera app (not a screenshot)?" → feeds signal-a; maps to CE 3.0 "device/session fingerprint."
3. *Carrier-delivery screenshot fails system-of-record reconciliation:* "Our carrier record doesn't match what you sent — can you request an official damage report from [carrier] and forward it by [date]?" → feeds signal-a; maps to "delivery confirmation with address match."
4. *High composite, low forensic confidence, no clean deny path:* "We're not able to approve this online — please bring the item to any Ridgeline store for an in-person look by [date]." → return-to-store; closes the loop without an extra photo request.

**4. Retro-audit sweep spec**

Lookback: trailing 60 days of *approved, no-return-required* refunds (15% of the 105,300 approved claims/year → ≈2,600 in the window). Re-score inputs: signal-a (generator-watermark/provenance + non-photo system-of-record checks) and signal-c (Yofi cross-merchant reuse). Rank by claim$ × composite risk, descending; route the top decile (~260 cases) to reviewer for pattern confirmation. Stop rule: hit rate (confirmed fraud ÷ reviewed) below **12.7%** ($12.06 reviewer cost/case ÷ $95 avg claim) — do not expand further down the ranked list. Privacy constraint: this is a re-read of evidence already lawfully held inside the 180-day EXIF retention window, not new collection. Guardrail: a retro flag opens a review, never an automatic clawback.

**5. Chargeback evidence mapping**

| Our signal | Scheme field | CE 3.0 / First Party Trust eligibility |
|---|---|---|
| Signal-a generator-watermark/provenance or C2PA failure | Device/session-fingerprint inconsistency | CE 3.0 eligible |
| System-of-record carrier reconciliation (Adyen/carrier API, not customer screenshot) | Delivery confirmation with address match | CE 3.0 + First Party Trust eligible |
| Signal-c internal block: prior undisputed orders, same device/ship-to | Prior order history / device consistency | CE 3.0 eligible |
| Reviewer SOP structured decision log + step-up thread | Communication log | First Party Trust eligible |
| Signal-c Yofi network-reuse flag | Supplemental fraud-network corroboration | Supports both frameworks as corroborating evidence |

**6. Reviewer SOP**

1. Ingest the composite score and four contributing signal sub-scores from the case queue (Ridgeline Trail Co. reviewer console header via `brand.banner`).
2. Compare the submitted photo against the catalog image and any prior claim photos on the account.
3. Check SKU-serial match for the smart-footwear/electronics-accessory cohort.
4. For non-photo artifacts, confirm the system-of-record reconciliation result before accepting.
5. Document the decision in structured fields (tier, signal scores, rationale, evidence collected).
6. Escalate claims ≥ $300 or a novel fabrication pattern to a senior reviewer.
7. Route ≥ 3 linked accounts (shared ship-to/device/payment across confirmed-fraud claims) to LP/asset protection as an organized-return-fraud ring.
8. Close the loop: log the outcome with the 180-day EXIF-retention flag applied.

**7. Privacy and retention checklist**

- EXIF used for the claim decision only; purged at 180 days.
- Faces in unboxing videos not stored; auto-blurred before human review.
- Appeal path is one click from the deny message.
- Refund-turnaround commitment (5 business days from `policies.refund_turnaround_commitment`) tracked weekly for auto-approve and auto-approve-w/-notation tiers.
- Retro-audit sweep re-reads evidence already lawfully held; the sweep itself triggers no new collection.
- Step-up request copy reviewed for non-accusatory tone before send.

**8. KPI scorecard**

| KPI | Baseline | Target | Rollback trigger |
|---|---|---|---|
| Refund-approval rate | 81% | Hold 78–83% | Monitor only |
| Step-up completion rate | n/a (new) | ≥ 65% | < 50% for 2 weeks → simplify template |
| Confirmed-fraud rate / 1,000 claims | n/a (new) | Decline quarter-over-quarter | Monitor only |
| False-decline rate (audit sample) | Target ≤ 0.4% | ≤ 0.4% | > 0.6% for 2 consecutive weekly cycles → rollback thresholds |
| Chargeback representment win rate | n/a (new) | ≥ 60% | < 45% for 2 cycles → review evidence mapping |
| Refund-time-to-credit | 5 business days (SLA) | Hold to SLA | Breach on > 10% of cases in a week → rollback |

**9. Config-utilization checklist**

1. `brand.banner` = "Ridgeline Trail Co." — step-up templates and reviewer SOP header.
2. `policies.refund_turnaround_commitment` = "5 business days from approval" — KPI + privacy-checklist SLA basis.
3. `payments.psp` = "Adyen" — cited in the chargeback representment section.
4. `loyalty.tiers` = Basecamp / Summit / Peak — Peak carve-out applied in the auto-approve tier.
5. `risk_appetite` = "moderate" — composite thresholds set at 0.20 / 0.40 / 0.70 rather than an industry median.
6. `image_forensics.c2pa_required_categories` = insulated outerwear > $300 AOV, technical footwear with embedded electronics, eyewear/sunglasses.
7. `chargeback_policy.evidence_paths` = configured, shared source with `agentic-checkout-fraud-shield` — pre-populated the mapping table above.
8. `behavioral_risk_score.external_vendor` = "UPS Happy Returns Return Vision" — 0.35 weight inside signal-c.
9. `non_image_inspection.vendor` = **none configured — FLAGGED.** Step-up path for serialized/high-AOV SKUs falls back to customer-photo/video only; recommend configuring before smart-footwear/electronics-accessory SKUs scale further.
10. `submitted_evidence.accepted_artifacts` = primary photo + carrier-delivery screenshots + complaint narratives (CS-approval screenshots not accepted) — each screened per signal-a.
11. `network_fraud_signal.vendor` = "Yofi" — 0.20 weight inside signal-c cross-merchant reuse.
