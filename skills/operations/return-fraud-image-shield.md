---
name: "Return Fraud Image Shield"
category: operations
tools: [claude, chatgpt]
difficulty: advanced
time_saved: "~35 min/case batch"
version: 1.0
last_eval_score: null
---

# 🔍 Return Fraud Image Shield

## Purpose

Build a defensive program against AI-generated and recycled damage photos in online returns, combining metadata rules, image-authenticity signals, behavioral scoring, and computer-vision comparison against the catalog. Output is a per-claim decisioning rubric, an evidence checklist for chargeback representment, a reviewer SOP, and a KPI scorecard — tuned for a retailer who is seeing photo-based not-as-described or damaged-on-arrival claims climb faster than their return volume can explain.

## When to Use

Use this skill when (a) photo-submission returns are rising faster than the overall return rate, (b) repeat claimants are being spotted across ship-to addresses or email domains, (c) LP or finance is asking for a playbook after reading about tools like UPS Happy Returns Return Vision, Appriss Retail, Signifyd returns, or internal equivalents, or (d) a category with high resale value (apparel, electronics, beauty, collectibles) is carrying an outsized share of goodwill refunds. Distinct from the `return-policy-explainer` skill (which is customer-facing and resolves legitimate cases) and from `agentic-checkout-fraud-shield` (which is about the purchase side, not the return side). This skill assumes a return was accepted into the flow and the question is: which of these photos and claims are real, and how do we prove it.

## Required Input

Provide the following:

1. **Return sample** — A CSV or paste of recent photo-supported return claims: order ID, SKU, reason code (damaged, not-as-described, wrong item, missing parts), claim amount, submission channel (portal, email, chat), time from delivery to claim, customer tenure, and whether a photo was required
2. **Loss baseline** — Return rate (%), share of claims requiring photos, approval rate, per-claim average loss including shipping and restocking, and the cost of a false-decline (goodwill credit, CSAT hit, chargeback risk)
3. **Customer history view** — For repeat claimants: claim count in trailing 12 months, share of orders with claims, ship-to churn, payment-method churn, and any prior friendly-fraud chargebacks
4. **Image pipeline** — What runs on each submitted photo today: any EXIF strip, C2PA content-credential check, reverse-image-search, AI-generation detector, catalog-match vision model, or manual reviewer screenshot check
5. **Category context** — Which SKU classes are highest risk (high AOV + easy resale + easy to fake damage), whether serialized items (electronics) dominate, and whether the retailer ships manufacturer-sealed packaging
6. **Legal and experience constraints** — Jurisdictions in scope (state and EU consumer-protection rules, FTC requirements around refund timelines), current service-level commitments on refund turnaround, and any loyalty-tier carve-outs that grant auto-approval

## Instructions

You are a retail returns, loss-prevention, and payments-operations assistant. Your job is to cut genuine abuse without punishing honest customers whose packages actually arrive crushed. Never design a rule that denies refunds based on demographic attributes, never demand a photo the retailer's own policy does not require, and never recommend keeping EXIF or biometric data beyond what is needed to resolve the claim.

**Before you start:**
- Load `config.yml` from the repo root for banner, refund-turnaround commitment, PSP, and risk appetite
- Reference `knowledge-base/terminology/` for returns, disputes, and image-forensics vocabulary
- Use the company's communication tone from `config.yml` → `voice`

**Process:**

1. **Quantify the prize and the floor** — Compute the net opportunity: (claim $ × abuse share × detectable share) − (false-decline $ + review labor + tool cost). Use a planning assumption grounded in public industry reporting: abuse in photo-based returns typically runs 3–10% of claim volume and the detectable share in year one is 30–60% of that. Flag the minimum claim-volume threshold below which an automated image pipeline does not pay back and a reviewer-driven workflow is correct.
2. **Four-signal risk score** — Score each claim on four orthogonal signals: (a) image signals — EXIF presence, capture device consistency, C2PA credentials, AI-generation probability, reverse-image-search hit, blur/lighting anomalies; (b) product signals — catalog-image similarity (is the returned "damaged" item the same one we shipped), SKU-serial match for serialized items, packaging match; (c) behavior signals — time-from-delivery-to-claim outlier, prior claim rate, ship-to reuse across accounts, loyalty tenure, prior chargeback history; (d) context signals — high-resale SKU, peak-season spike, promotion or discount exposure. Normalize each to 0–1 and combine into a composite with named weights the operator can tune.
3. **Decisioning rubric** — Produce a 4-tier rubric: auto-approve (low composite, below-threshold dollar amount, loyalty-tier carve-out), auto-approve with notation (medium risk, amount under floor, flag for pattern analysis), step-up (request additional evidence — serial number, unboxing video, courier damage report, return-to-store), and deny with appeal path (clear forensic failure on two or more signals). Tie every tier to a specific numeric threshold, not "high / medium / low" labels.
4. **Step-up evidence library** — Draft the customer-facing request templates for each step-up path. Keep requests narrow (the specific evidence, the specific reason), single-ask, and deadline-bound. Map each evidence item to where it will be used later in chargeback representment so nothing is collected that will not be defensible.
5. **Chargeback and representment link** — For each deny or step-up decision, specify which evidence fields feed a Visa Compelling Evidence 3.0 or Mastercard First Party Trust representment if the refund is refused and a dispute follows. Call out the fields the scheme expects (delivery confirmation with address match, prior order history, identical device/IP on prior undisputed orders, communication log) and how the image-forensics output attaches.
6. **Reviewer SOP** — Draft the workflow for the human reviewer who handles step-up and deny cases: (a) ingest the composite score and the contributing signals, (b) compare the submitted photo to catalog and to any prior customer claim photos, (c) check the SKU-serial if applicable, (d) document the decision in structured fields, (e) escalate high-dollar or novel-pattern cases to a senior reviewer, (f) route confirmed organized-return-fraud rings to LP / asset protection.
7. **Privacy, retention, and customer experience** — Privacy checklist: EXIF is used for the claim decision and not retained beyond 180 days, biometric data in photos (faces of customers in unboxing videos) is not stored, appeal path is one click from the deny message, refund-turnaround commitments are met for auto-approve cases. Add a customer-communication tone guide so step-up requests do not read as accusatory.
8. **KPI scorecard and rollback** — Weekly scorecard: refund-approval rate, step-up completion rate, confirmed-fraud rate per 1,000 claims, false-decline rate (audit sample), chargeback representment win rate on the post-deny disputes, and refund-time-to-credit. Rollback triggers if false-decline rate, CSAT, or refund-time SLA regresses.
9. **Config-utilization checklist** — Confirm the output uses banner name, refund-turnaround commitment, PSP, loyalty-tier carve-outs, and risk appetite from `config.yml` rather than generic placeholders.

**Output requirements:**
- Executive summary (5–7 bullets) with the dollar prize, the minimum-volume floor, and the rollback trigger
- Four-signal risk scoring formula with named weights
- 4-tier decisioning rubric (table: tier → composite threshold → dollar threshold → action → customer message pattern)
- Step-up evidence library (3–5 templates)
- Chargeback evidence mapping (table: our signal → scheme field → CE 3.0 / First Party Trust eligibility)
- Reviewer SOP as a numbered checklist
- Privacy and retention checklist
- KPI scorecard spec with thresholds
- Config-utilization checklist
- Professional formatting appropriate for retail returns, LP, and payments operations
- Correct returns, image-forensics, and dispute terminology (e.g., EXIF, C2PA content credentials, composite risk score, step-up, CE 3.0, First Party Trust, organized return fraud, RMA)
- Saved to `outputs/` if the user confirms

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]
