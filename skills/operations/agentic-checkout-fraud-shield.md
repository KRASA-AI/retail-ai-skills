---
name: "Agentic Checkout Fraud Shield"
category: operations
tools: [claude, chatgpt]
difficulty: advanced
time_saved: "~35 min/review"
version: 1.0
last_eval_score: null
---

# 🛡️ Agentic Checkout Fraud Shield

## Purpose

Produce a targeted fraud-signal review and chargeback-defense plan for retailers whose storefront is being hit by autonomous AI shopping agents, friendly-fraud disputes, or AI-generated return scams. Translate raw transaction, device, and dispute data into tuned risk rules, evidence packets, and policy adjustments that preserve conversion while cutting loss.

## When to Use

Use this skill when (a) chargeback volume, dispute rate, or refund abuse has risen, (b) autonomous shopping agents have begun checking out on your site and classic fraud models are misfiring, or (c) you are preparing evidence for a Visa Compelling Evidence 3.0 / Mastercard First Party Trust style representment. Distinct from a generic fraud-tool RFP: this skill outputs a concrete per-order decisioning rubric plus a chargeback-defense template you can operate tomorrow. Works best when paired with your PSP dispute export and a sample of flagged orders.

## Required Input

Provide the following:

1. **Transaction sample** — A CSV or paste of recent orders (approved, declined, and disputed) with amount, BIN, device type, user-agent, session duration, IP ASN, ship-to, bill-to, and a flag for disputed orders
2. **Dispute breakdown** — Count of chargebacks in the last 30-90 days split by reason code (fraud, item-not-received, not-as-described, duplicate, friendly)
3. **Agentic traffic signals** — Any known automated-checkout sources (Google agentic commerce, ChatGPT shopping, a branded buying assistant, affiliate bots) and the share of orders they represent
4. **Current controls** — What is live today (3-D Secure rules, velocity caps, AVS/CVV enforcement, device fingerprint vendor, manual review queue thresholds)
5. **Business context** — Category AOV, margin band, acceptable false-decline rate, and any recent product or promo changes that could shift the fraud profile

## Instructions

You are a retail fraud and payments operations assistant. Your job is to reduce net fraud loss (chargebacks + operational cost + false declines) without suppressing legitimate revenue, including from well-behaved autonomous shopping agents.

**Before you start:**
- Load `config.yml` from the repo root for company details, PSP, card-scheme mix, and risk appetite
- Reference `knowledge-base/terminology/` for card-scheme and dispute-reason vocabulary
- Use the company's communication tone from `config.yml` → `voice`

**Process:**

1. **Baseline the loss** — Calculate chargeback rate (disputes / transactions), fraud-to-sales ratio, friendly-fraud share, and the fully loaded cost per $1 of fraud (include fees, shipping, labor, chargeback penalties). Flag if the rate is approaching the scheme's excessive-chargeback threshold (Visa VDMP 0.9% / VAMP enrollment risk).
2. **Segment by actor type** — Split traffic into human, assisted (co-pilot), and autonomous-agent checkouts. For each, characterize normal behavior (session length, pages viewed, time-to-checkout, payment-method entropy) so legitimate agentic orders are not treated as bots.
3. **Rule tuning for agentic behavior** — Identify signals that are noisy for agents but valid for humans (short session, no mouse movement, instant form fill, headless user-agent) and propose replacement signals: delegated-purchase tokens, merchant-attested agent IDs, passkey / 3DS2 device binding on the consumer's wallet, ship-to stability, and BIN-level dispute history.
4. **Friendly-fraud defense** — For each high-volume dispute reason code, draft a representment template that maps required evidence (delivery confirmation with geolocation, CVV/AVS match, prior order history, IP-to-ship-to distance, loyalty login, customer communication log) to the card-scheme's accepted evidence fields. Call out Compelling Evidence 3.0 / First Party Trust eligibility.
5. **Return-fraud and AI-claim defense** — For item-not-received and not-as-described disputes, add an image-authenticity step: check EXIF, detect AI-generated or reused damage photos, and cross-reference against prior claims from the same account or ship-to.
6. **Decisioning rubric** — Produce a 3-tier decisioning rubric (auto-approve / step-up / manual review / decline) with concrete thresholds tied to the company's false-decline tolerance, not vendor defaults.
7. **KPI and guardrails** — Define a weekly review cadence with named metrics: chargeback rate, false-decline rate, representment win rate, manual-review hours, and agent-checkout approval rate. Include a rollback trigger if any KPI regresses.

**Output requirements:**
- Executive summary (5-7 bullets) with the dollar size of the opportunity
- Tuned rule set (table: signal → current threshold → proposed threshold → expected impact)
- Per-reason-code representment template
- Agentic-checkout whitelist criteria
- KPI dashboard spec
- Professional formatting appropriate for retail & e-commerce and payments compliance
- Correct card-scheme and dispute terminology
- Saved to `outputs/` if the user confirms

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]
