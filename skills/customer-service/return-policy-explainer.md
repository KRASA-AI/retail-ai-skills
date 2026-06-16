---
name: "Return Policy Explainer"
category: customer-service
tools: [claude, chatgpt]
difficulty: beginner
time_saved: "~7 min/case"
version: 2.4
last_eval_score: 8.9
---

# 🔄 Return Policy Explainer

## Purpose

Generate clear, customer-facing explanations for return, exchange, refund, and warranty scenarios — with a full RMA + reverse-logistics-path + refund-method decision, dual-path (strict / goodwill) draft, RFID / serialized-item authentication step, and a fraud / dispute guardrail with a named bridge to `return-fraud-image-shield` — so frontline agents resolve the case in one touch, set correct expectations, route image-claim cases through the four-signal score before goodwill is granted, and protect the business from policy abuse and chargeback escalation.

## When to Use

Use this skill when a customer asks about returning or exchanging a product, when you need to draft return-policy language for your website or emails, when a support agent needs a quick, accurate response for a specific return scenario, or when a dispute or chargeback has been filed and you need a deflection-first response. Distinct from Customer Service Reply (general inquiries) and Agentic Checkout Fraud Shield (transaction-level fraud defense): this skill is purpose-built for the return / exchange / warranty conversation with policy-aware logic, restocking-fee math, reverse-logistics-path selection, and an explicit handoff to `return-fraud-image-shield` when image-claim signals trip. Works best when paired with your published return policy, carrier label defaults, the customer's order record, and (if available) the recent return-fraud-image-shield score for this account.

## Required Input

Provide the following:

1. **Scenario type** — Return, exchange (same SKU different variant), exchange (different SKU), refund, store credit, warranty claim, price-adjustment / post-purchase price-match, or pre-dispute chargeback threat
2. **Product and order details** — SKU / category, order number, order date, order value, payment method (card, BNPL, gift card, store credit, loyalty points, ACH), current condition (sealed, opened, used, damaged in transit, defective out of box, well-worn), serialization status (IMEI / serial / RFID-tagged), and number of prior returns on this account in the trailing 90 days
3. **Return window status** — Days elapsed vs. standard window; extended-window flag (holiday extension, gift-recipient window, VIP / loyalty tier, protected category like apparel fit guarantee, EU 14-day cooling-off rights)
4. **Special circumstances** — Gift purchase, final-sale / clearance, custom or personalized, hazardous / perishable / intimate apparel, international order, post-tariff lane (US Section 321 / EU IOSS / UK low-value rule changes), subscription, promotional bundle, mixed PO with kept + returned items, serialized item (IMEI / serial number / authentication required for electronics, watches, footwear)
5. **Desired outcome** — What the customer is requesting vs. what policy allows (flag the gap)
6. **Channel and tone cues** — Channel (email, chat, phone note, social DM, marketplace message, dispute response) and signal of customer sentiment (neutral, frustrated, threatening dispute)
7. **Image-claim signal (if applicable)** — If the customer has uploaded photos to support a damaged / defective / not-as-described claim, the most recent `return-fraud-image-shield` four-signal score and tier (auto-approve / step-up / manual-review / decline) for this case; otherwise null

## Instructions

You are a retail customer service policy specialist. Your job is to translate return policies into clear, empathetic, customer-facing language that resolves the situation in one touch while protecting margin, inventory quality, and chargeback ratio. Never grant a goodwill exception on an image-claim case until the `return-fraud-image-shield` score has been read and the tier is auto-approve or step-up; for manual-review tier, route to the supervisor authority lane and request the step-up evidence before drafting.

**Before you start:**
- Load `config.yml` from the repo root and read these fields (mark any that are missing so the merchant can backfill before the reply is sent):
  - `return_policy.windows` — standard, gift, VIP, and EU windows; the post-tariff buyer-pays-return-shipping flag; the fit-guarantee category list
  - `return_policy.restocking_fee_schedule` — fee % by condition and reason
  - `return_policy.return_shipping_paid_by` — who pays return shipping, by reason × tier
  - `return_policy.refund_method_matrix` — channel × payment × tier, with the SLA per cell
  - `return_policy.reverse_logistics_path` — carrier home-pickup, in-store drop, third-party kiosks (Happy Returns / Narvar / Loop / The Bay Returns Bar), prepaid QR vs. printed label, and BOPIS counter — keyed by category and by zip
  - `escalation_thresholds` — agent / supervisor / director goodwill authority bands
  - `loyalty.tiers` — tier names and entitlements
  - `payment_methods` — accepted methods and their refund timelines
  - `warehouse.rma_intake_address` — return address for the RMA block
  - `serialization.authentication_required_categories` — electronics, watches, footwear, luxury
  - `brand.voice` — tone for the customer-facing reply
- Reference `knowledge-base/terminology/` for RMA, restocking fee, store credit, return window, chargeback representment, reverse logistics, and carrier vocabulary
- Reference `knowledge-base/regulations/` for the post-tariff de-minimis / IOSS / VAT rule changes that affect cross-border return shipping responsibility
- Use the company's communication tone from `config.yml` → `voice`

**Process:**

1. **Scenario classification** — Tag the case into one of 10 return types: (1) standard return within window, unopened; (2) standard return within window, opened / used; (3) late return outside window; (4) defective / damaged out of box; (5) damaged in transit; (6) exchange same SKU different size or color; (7) exchange to different SKU; (8) gift return (no receipt, different recipient — applies the longer gift-recipient window from config); (9) warranty claim (manufacturer defect past return window); (10) price-adjustment / post-purchase price-match. Tag any fraud-risk signals separately: 3+ returns in 90 days, serial-number mismatch, reship-to-address change, box-only return (empty), AI-generated damage photo flagged by `return-fraud-image-shield`. These trigger the strict path and an internal note, not customer-facing friction.

2. **Image-claim bridge to return-fraud-image-shield** — If the case includes customer-supplied photos for a damaged / defective / not-as-described claim, route through the four-signal score (image forensics: EXIF / C2PA / AI-gen detector / reverse-image-search; product: catalog-match vision + SKU-serial; behavior: claim rate / ship-to reuse / chargeback history; context: high-resale SKU / peak-season / promo exposure) and the 4-tier decisioning rubric:
   - **Auto-approve** → proceed to step 3 with the goodwill path open
   - **Step-up** → request the named additional evidence (close-up of damage with timestamped object in frame, original packaging photo, serial-number close-up) and pause the draft until received
   - **Manual-review** → escalate to supervisor authority with the score breakdown in the internal note; draft a neutral holding reply
   - **Decline** → strict path only, with a non-accusatory denial; document the four-signal breakdown in the internal note for representment evidence
   Never grant a goodwill gesture on an image-claim case below the auto-approve tier without an explicit supervisor override that is logged.

3. **Policy lookup, refund-method, and reverse-logistics-path decision** — Match the scenario against `config.yml` → `return_policy` and decide four things in order:
   - **Eligibility:** eligible / eligible with exception / ineligible
   - **Refund method × channel × SLA matrix** — pick the cell from `return_policy.refund_method_matrix`. Standard SLAs by payment method:
     - Card → 3–5 business days from RMA receipt to refund posted
     - BNPL (Affirm / Klarna / Afterpay / PayPal Pay-in-4) → per-provider timeline, typically 3–10 business days; surface the named provider's SLA, not a generic window
     - Gift card → instant on RMA receipt
     - Store credit → instant on RMA receipt
     - Loyalty points → instant on RMA receipt
     - ACH / bank transfer → 5–7 business days
   - **Restocking fee:** compute as policy % × item subtotal; waive automatically for defective / damaged / wrong-item-shipped / VIP-tier / BOPIS-counter drop (BOPIS waives restocking by default per the 2026 buyer-friction policy); apply for opened non-defective returns and late returns
   - **Reverse-logistics path:** pick the lowest-friction path the customer is eligible for from `return_policy.reverse_logistics_path`:
     - Third-party kiosk (Happy Returns / Narvar Box / Loop / The Bay Returns Bar) — printer-free, drop-off in 5 min, lowest customer effort, available by zip
     - BOPIS counter — in-store drop with associate verification; restocking auto-waived
     - Carrier home-pickup (UPS / USPS / DHL / FedEx home-pickup) — for high-AOV or VIP tier
     - Prepaid QR label — printer-free; customer drops at carrier counter
     - Printed prepaid label — fallback when QR is not supported
     - Customer-paid label — only when policy explicitly assigns return shipping cost to the buyer (buyer's-remorse outside extended window, or post-tariff lane where the buyer-pays-return-shipping flag is set)

4. **Dual-path draft (strict + goodwill)** — When the case falls in a gray area (e.g., 5 days past window on a $400 item, loyalty-tier-gold customer, or step-up image-claim tier), draft **both**:
   - **Strict-policy draft** — upholds the published policy with a helpful alternative (store credit instead of cash refund, 20% off next order)
   - **Goodwill draft** — one-time exception with a clear "this is a one-time accommodation" line so the next agent does not re-set the precedent
   Name the decision authority (agent self-serve up to $X, supervisor $X–$Y, director > $Y) from `config.yml` → `escalation_thresholds`. This keeps the agent in policy lanes.

5. **RMA mechanics with RFID / serialized-item authentication** — Produce the concrete next-step block: RMA number format, return shipping address from config, carrier and label type per the chosen reverse-logistics path (kiosk QR / BOPIS receipt / prepaid QR / printed / home-pickup window), packaging requirements (original box for electronics, tags attached for apparel, anti-tamper seal for luxury, serial-number visible in photo for serialized items). For categories in `config.serialization.authentication_required_categories` (electronics, watches, footwear, luxury, RX-eligible), require the RFID / serial / IMEI scan or visible-in-photo verification at the kiosk or BOPIS counter — and call out that goods returned with a missing or mismatched serial are routed to the LP / authentication queue, not the standard refund flow. Include a four-image photo request if the item is described as damaged or defective (front, back, damage close-up, packaging) when the case did not come in with photos already.

6. **Exception handling and chargeback deflection** — If the customer has signaled a chargeback or dispute ("I'll call my bank"), include a deflection paragraph that: (a) acknowledges the frustration, (b) offers the fastest path to resolution in writing, (c) notes that a chargeback will pause the refund while the bank investigates (15–45 days), and (d) preserves the evidence trail (AVS/CVV match confirmation, delivery tracking, prior communication log) per Visa Compelling Evidence 3.0 / Mastercard First Party Trust. Do not threaten; do document. If the case has crossed the fraud threshold per the four-signal score, call out the named handoff to `return-fraud-image-shield` and `agentic-checkout-fraud-shield` in the internal note (not the customer-facing reply) so representment evidence is preserved.

7. **Proactive deflection and upsell** — Close with one-liner future-question deflection ("tracking updates post automatically to your account") and, where appropriate and non-manipulative, a soft re-engagement (size swap link, 10% off reorder of the correct item, subscribe-to-back-in-stock, BOPIS-counter pickup option to skip the return shipping line altogether). Never offer incentives that could be read as coercion to withdraw a complaint.

8. **Internal note block** — Separate from the customer-facing reply, produce an internal note for the CRM / helpdesk: scenario tag, refund method chosen with SLA cell cited, restocking fee applied with auto-waive reason, reverse-logistics path picked with friction tier, return-shipping responsibility, goodwill flag (yes / no / one-time), return-abuse score flag if tripped, return-fraud-image-shield tier (if image claim) and the four-signal breakdown, RFID / serialized-item authentication status, and the RMA number. This is what the next agent reads first.

9. **Config-utilization checklist** — Confirm the output uses `return_policy.windows`, `return_policy.restocking_fee_schedule`, `return_policy.return_shipping_paid_by`, `return_policy.refund_method_matrix`, `return_policy.reverse_logistics_path`, `escalation_thresholds`, `brand.voice`, `loyalty.tiers`, `payment_methods`, and `serialization.authentication_required_categories` from `config.yml` rather than generic placeholders. Cite the named reverse-logistics provider (Happy Returns / Narvar / Loop / The Bay Returns Bar / BOPIS counter / prepaid-QR carrier) on the customer-facing reply and the named refund-method-matrix cell + SLA on the internal note.

**Output requirements:**
- **Customer-facing reply** — channel-ready, on-brand, with clear next steps, timeline, named reverse-logistics path, payment-method-appropriate refund SLA, and (if gray area) both strict and goodwill drafts clearly labeled
- **RMA block** — RMA number, return address or kiosk locator, carrier / label type, packaging rules, RFID / serialized-item authentication requirement (if applicable), SLA from received to refund
- **Restocking-fee line-item math** — item subtotal, fee %, fee $, net refund, auto-waive reason if waived (defective / VIP / BOPIS counter)
- **Refund-method × channel SLA cell** — payment method × tier × named provider SLA, named on the internal note so the agent and the customer see the same number
- **Reverse-logistics path** — friction tier (kiosk / BOPIS / home-pickup / QR / printed / customer-paid) with the named provider
- **Image-claim bridge** — `return-fraud-image-shield` score and tier if applicable, with the four-signal breakdown on the internal note
- **Chargeback-deflection paragraph** — only when dispute language is present in the customer input, with named handoff to `return-fraud-image-shield` and `agentic-checkout-fraud-shield` in the internal note when the fraud threshold is crossed
- **Internal note** — scenario tag, decisions made, abuse-flag status, image-shield tier, authority level required
- **Config utilization checklist** — names the 10 config fields used (return_policy.windows, return_policy.restocking_fee_schedule, return_policy.return_shipping_paid_by, return_policy.refund_method_matrix, return_policy.reverse_logistics_path, escalation_thresholds, brand.voice, loyalty.tiers, payment_methods, serialization.authentication_required_categories) so the output is traceable to the merchant's actual rules
- Correct terminology (RMA, restocking fee, store credit, return window, representment, keep-it-and-refund, CE 3.0, FPT, Happy Returns, Narvar, Loop, BOPIS, RFID, IMEI, IOSS, EU 14-day cooling-off)
- Professional formatting appropriate for retail customer service
- Saved to `outputs/` if the user confirms

## Example Output

> Reference run. **Input:** Gift return of a $189 pair of running shoes (serialized footwear), order placed 38 days ago (standard window 30 days, gift-recipient window 45 days — within window), gift recipient has no receipt, Gold loyalty tier, customer uploaded two photos claiming a sole-separation defect, channel = email, sentiment = neutral. `return-fraud-image-shield` score on this account = **step-up** tier (image forensics clean; behavior signal elevated — 2 prior returns in 90 days).

**Scenario tag:** (8) gift return × (4) defective-out-of-box, image-claim present → **step-up** tier. Within gift-recipient window (38 ≤ 45). Footwear is in `serialization.authentication_required_categories`.

**Image-claim bridge:** Tier = step-up. Draft paused for named evidence: timestamped close-up of the sole separation with a coin or ruler in frame, plus a photo of the serial/RFID tag on the tongue. Goodwill path remains closed until evidence is received (account is below auto-approve on behavior signal).

**Customer-facing reply (email):**

> Subject: Your return for order #RS-44820 — quick photo and you're set
>
> Hi Dana,
>
> Thanks for flagging the sole separation on the Cascade Trail runners — that's not the experience we want anyone walking out of the box with, and because this is a gift we've got you within our extended 45-day gift window, so you're fully covered.
>
> To get your replacement (or refund) moving, could you reply with two quick photos: (1) a close-up of the separated sole with a coin in the frame for scale, and (2) the serial tag on the inside of the tongue. Footwear is serialized on our side, so that tag is all we need to verify and skip any back-and-forth.
>
> Once those land, here's the path: drop the shoes at any **Happy Returns Bar** near 30309 — no box or printer needed, just the QR code we'll send, about a 5-minute stop. As a defective item there's **no restocking fee**, and your refund posts to the **original gift card within 1 business day** of the bar scanning it. Prefer a replacement pair instead? Say the word and we'll ship the same size at no charge the moment the photos check out.
>
> Reply here with the two photos and I'll take it from there.
>
> Warmly,
> Maya — Riverside Outfitters Customer Care

**RMA block:** RMA #RS-44820-R1 (issued on photo receipt) · Path: Happy Returns Bar drop-off (prepaid QR, printer-free) · Locator keyed to reference zip 30309 · Packaging: serial tag must be visible at scan; original box not required for kiosk path · Auth: footwear serial/RFID scan at bar; serial mismatch → LP/authentication queue, not standard refund · SLA: refund posts ≤ 1 business day from bar scan (gift card, instant on receipt per matrix).

**Restocking-fee math:** Item subtotal $189.00 × fee 0% = **$0.00** (auto-waived — reason: defective; would also waive on Happy Returns counter drop). Net refund: $189.00 to original gift card.

**Refund-method × channel SLA cell:** `refund_method_matrix[gift_card][email][gold]` → instant-on-RMA-receipt (gift card). Cited on internal note.

**Reverse-logistics path:** Happy Returns Bar (kiosk tier — lowest friction; eligible by zip 30309 and Gold tier).

**Image-claim bridge:** `return-fraud-image-shield` tier = step-up; four-signal breakdown on internal note (forensics clean / product pending serial scan / behavior elevated 2× returns-90d / context: footwear mid-resale). Goodwill held until step-up evidence received.

**Internal note (CRM):** Tag 8+4 gift/defective · image tier STEP-UP (evidence requested, draft paused on goodwill) · refund method gift_card, cell `[gift_card][email][gold]` instant-on-receipt · restocking 0% auto-waived (defective) · path Happy Returns Bar (kiosk) · return-shipping = merchant-paid (defective) · goodwill flag: NO (not granted; tier below auto-approve) · abuse score: behavior elevated, not tripped · serial-auth: required at bar scan · RMA RS-44820-R1 · authority: agent self-serve (no goodwill gesture issued).

**Config-utilization checklist:** ✅ `return_policy.windows` (gift 45d applied) · ✅ `return_policy.restocking_fee_schedule` (0% defective) · ✅ `return_policy.return_shipping_paid_by` (merchant, defective) · ✅ `return_policy.refund_method_matrix` (cell cited) · ✅ `return_policy.reverse_logistics_path` (Happy Returns Bar) · ✅ `escalation_thresholds` (agent self-serve) · ✅ `brand.voice` (warm, concise) · ✅ `loyalty.tiers` (Gold) · ✅ `payment_methods` (gift card) · ✅ `serialization.authentication_required_categories` (footwear → serial scan). No fields missing.

## Notes

- Image-claim cases never skip `return-fraud-image-shield`. The bridge is the load-bearing addition in v2.2 — goodwill on a manual-review tier case is the same failure mode as a missed `agentic-checkout-fraud-shield` decline at purchase, paid out the back door.
- The reverse-logistics path matrix is a customer-experience lever, not a cost lever. A kiosk drop-off costs the merchant more in vendor fee than a customer-paid label, but the conversion-on-replacement-purchase delta typically more than covers it. The skill should pick the path that minimizes customer friction within the policy lane, not the one that minimizes line-item cost.
- Post-tariff buyer-pays-return-shipping is a 2026 reality on cross-border lanes that have lost de-minimis treatment. The skill must surface the cost to the customer up front; do not paper over a $30 return-shipping bill in a 3-line reply.
- RFID / serialized-item authentication on electronics, watches, footwear, and luxury is what turns a return desk from a refund-and-rebox station into an authentication checkpoint. Without this step, the LP queue picks up the failure 14 days later when the gray-market unit is already on a marketplace.
