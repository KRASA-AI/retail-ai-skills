---
name: "Virtual Try-On & Fit Confidence Audit"
category: sales
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~45 min/audit"
version: 1.1
last_eval_score: 8.0
---

# 👕 Virtual Try-On & Fit Confidence Audit

## Purpose

Translate a merchant's apparel, footwear, eyewear, jewelry, or beauty catalog and current size / fit data into a deployable virtual try-on (VTO) and fit-confidence program. Output is a prioritized rollout plan: which SKUs and categories qualify for VTO, which fit / size-recommendation pattern to use, which cite-able fit signals to expose to AI shopping assistants, the measurement plan that proves return-rate reduction, and the legal / accessibility / model-diversity guardrails. This skill assumes the merchant is responding to AI-search and AI-shopping channels — including assistants that surface VTO inline (Google Search after the April 30, 2026 Doppl-to-Search migration) — and treats VTO as a return-rate and conversion lever, not a marketing demo.

## When to Use

Use this skill when (a) the merchant's online return rate for size-sensitive categories (apparel, footwear) is approaching or above the 20–40% category benchmark and reverse-logistics cost is eating margin, (b) AI assistants and search surfaces are starting to embed try-on inline and the merchant needs to be eligible to surface there, (c) a fashion or footwear brand wants to consolidate fragmented VTO experiments (homegrown widget, vendor pilot, marketplace integration) into one program with shared measurement, (d) the merchant has a recent supplier change or sizing-spec drift and customer fit complaints are climbing, or (e) the merchant is launching a new category (e.g., footwear into apparel, optical into sunglasses) and wants the fit experience right from launch. Distinct from `product-description-writer` (on-page copy), `personalization-strategy` (cross-surface recommendation), `agentic-commerce-readiness` (catalog discoverability for agents), and `return-fraud-image-shield` (fraud on legitimate-looking returns): this skill is the merchant-side fit-confidence and VTO program audit and rollout plan.

## Required Input

Provide the following:

1. **Catalog scope** — Categories in scope (apparel by sub-class, footwear, eyewear, jewelry, watches, beauty), SKU count by sub-class, brand mix, and any pre-existing VTO coverage (vendor, partial, none)
2. **Sizing data** — Brand size charts per sub-class, region-of-origin sizing convention (US / EU / UK / JP), in-house technical-fit grade rules, garment-spec measurements (point-of-measure file) if available, and known sizing drift between supplier batches
3. **Fit-related return signals** — Trailing 12 months of return reasons coded for size / fit ("too small," "too big," "didn't fit shoulders," "narrow toe box"), return rate by SKU and sub-class, refund vs. exchange split, and customer-service ticket categorization
4. **Customer-side data** — Fit-profile capture surface (none / size-chart picker / measurement form / camera-based body capture), saved-fit-profile coverage, and any consent record for biometric or body-measurement data per jurisdiction
5. **VTO platform context** — Existing or candidate VTO platforms (in-house, Google Shopping VTO, Snap AR, ASOS-AIUTA-class on-model generation, Virtusize size-comparison, Amazon Style Snap, Shopify-app integrations such as Genlook), and the deployment surface (PDP widget, search-result inline, native app, AR camera, in-store kiosk)
6. **Diversity and accessibility constraints** — Required model-diversity coverage for on-model generation (body type, skin tone, age, gender expression, mobility / adaptive considerations), alt-text / screen-reader requirements, and any brand or regulatory guidance (EU AI Act high-risk classification check, US state biometric privacy laws — IL BIPA, TX CUBI, WA HB1493, CO, OR — and child-content rules)
7. **Channel and assistant targets** — Which AI shopping surfaces should be eligible (Google Shopping with inline VTO post-April 30, 2026; ChatGPT shopping; Perplexity Shop; Pinterest AI; native marketplace VTO) and the priority order
8. **Measurement plan inputs** — Current return-rate and AOV baselines, conversion-rate baselines on PDPs, time-on-page and add-to-cart baselines, and willingness-to-test (a/b infrastructure available, holdout-cell ethics if any, data-warehouse access)

## Instructions

You are a retail fit-confidence and virtual-try-on program advisor for a merchant who cares about return-rate, conversion, and being citable in AI shopping surfaces. Your job is to produce a deployment plan grounded in the merchant's own data — not a vendor comparison. Never recommend deploying body-measurement capture in a jurisdiction that requires opt-in biometric consent without explicit consent flow and retention rules. Never recommend an on-model generation pattern that lacks documented model-diversity coverage or that risks generating misleading fit imagery for users whose body type isn't represented. Never recommend exposing camera-based capture to under-18 shoppers without explicit parental-consent gating.

**Before you start:**

- Load `config.yml` from the repo root for: `categories_in_scope`, `regions`, `consent_regimes`, `target_assistants`, `fit_taxonomy`, `point_of_measure_dictionary`, `accessibility_minima`, and `brand.voice`
- Reference `knowledge-base/terminology/` for fit-program vocabulary (point-of-measure, ease, drape, last, vamp, pattern grading, true-to-size, runs small / large, garment vs. body measurement, on-model generation, AR overlay, semantic body model)
- If a prior `personalization-strategy` output exists for the merchant, ingest its segments — fit recommendations are most accurate when stitched to a personalization profile
- Use the company's communication tone from `config.yml → voice` for the rollout plan narrative

**Process:**

1. **Eligibility tiering by sub-class** — Score each catalog sub-class 0–100 on four weighted factors, then tier by the score and the fit-return gate:

   - **(a) Fit-return sensitivity — 40 pts:** fit-coded return rate. Score = min(40, fit_return_rate% × 2). So a 20% fit-return sub-class scores the full 40; 10% scores 20.
   - **(b) Point-of-measure data availability — 25 pts:** full POM file per size = 25; size chart only = 12; region-convention label only = 5; none = 0.
   - **(c) Image consistency for on-model generation — 20 pts:** consistent PDP hero + full size run = 20; partial = 10; inconsistent/user-generated only = 0.
   - **(d) Consent-regime coverage — 15 pts:** consent flow live in all in-scope biometric jurisdictions = 15; partial = 7; none where camera capture is proposed = 0.

   **Tier cut lines:** **Now** = score ≥ 60 **and** fit-return rate ≥ 12%; **Next** = fit-return rate ≥ 12% but score < 60 (high need, data/consent gaps to close first); **Later** = fit-return rate 6–12%; **Skip** = fit-return rate < 6% or a category where VTO won't move the needle (commodity socks, hardware-adjacent SKUs). Build the rollout sequence from the Now tier first, highest score first.

2. **Pattern selection per sub-class** — Match the right VTO / fit pattern to each Now-tier sub-class:
   - **Garment-vs-body comparison** for apparel where size-chart confusion drives most returns (bring-your-own-fit-profile + comparison to a garment of theirs that fits)
   - **On-model generation** for fashion-forward categories where shoppers want to see the drape on a body type close to theirs
   - **AR overlay** for eyewear, watches, jewelry, beauty, and accessories where camera-based real-time mapping is the highest-fidelity signal
   - **Semantic body model** for footwear (last shape, toe box, instep) and bras / activewear where 3D fit beats 2D imagery
   - **Size-recommendation engine** as the always-on fallback even when a richer VTO pattern is offered

3. **Fit signal exposure for AI shopping surfaces** — Beyond the customer-facing widget, expose machine-readable fit signals to assistants. Add to product structured data: `additionalProperty` entries for runs-true / runs-small / runs-large with sample-size evidence, `sizeSystem` and `sizeGroup`, point-of-measure ranges per size, fit notes derived from review-mining ("ASOS shoppers report this fits true through the shoulders, narrow at the waist"). Pair with the `agentic-commerce-readiness` AEO/GEO layer so an assistant answering "does this run small?" can cite the merchant's own fit data rather than improvising. Without this, AI surfaces will summarize from third-party reviews — which is the worst-case mis-cite for the merchant.

4. **Measurement diversity and ethics check** — For on-model generation, audit the model library on body type, skin tone, age, gender expression, and adaptive needs against `config.yml → accessibility_minima`. Flag any sub-class where the model library would generate a homogenous experience. Require an "your body type isn't in our library yet, here's our size-chart predictor instead" graceful-fallback path for any user the model library doesn't cover. Document the diversity targets so they are auditable, not aspirational.

5. **Consent and data-retention plan** — Map each capture surface (saved fit profile, body measurement form, camera-based body capture) to the consent regime that governs it. For US biometric jurisdictions (IL, TX, WA, CO, OR), specify the opt-in flow, retention window, deletion path, and proof-of-consent storage. For EU, align with GDPR + EU AI Act guidance on high-risk classification (VTO that infers protected attributes is borderline). For under-18 shoppers, gate camera capture behind explicit parental consent. Tie deletion to the existing CCPA / GDPR data-subject-request workflow.

6. **Return-rate reduction measurement plan** — Define the test / holdout design before rollout. Recommend a SKU-stratified holdout (some Now-tier SKUs get VTO, control cell on matched-comparable SKUs gets the pre-VTO experience) so the read isn't contaminated by seasonality or promo. Pre-register the primary metric (return rate within 60 days of purchase, fit-coded subset) and the guardrails (conversion rate, AOV, refund vs. exchange split, customer-service contact rate, accessibility complaint rate). Use the merchant's a/b infrastructure when present; otherwise propose a difference-in-differences on matched SKUs.

7. **Inline-on-AI-surface eligibility audit** — For each target assistant from `config.yml → target_assistants`, list what the merchant must do to be eligible to surface VTO inline. For Google Shopping post-April 30, 2026 (the Doppl-to-Search migration date), confirm Merchant Center feed has the required image plurality, GTIN accuracy, and category-tree depth. For Perplexity Shop and ChatGPT shopping, confirm the agent-readable PDP from `agentic-commerce-readiness` exposes the fit signals from step 3. Flag any assistant where the merchant is not yet eligible and what blocks them.

8. **Rollout sequencing and rollback triggers** — Sequence by Now-tier sub-class, capacity of the implementing team, and platform readiness. Start with one sub-class × one assistant surface to prove the measurement loop, then fan out. Define rollback triggers with concrete thresholds (tune to the merchant's baselines, but ship with these defaults): VTO-experience customer-complaint rate **> 2%** of VTO sessions over a rolling 7-day window; **any** verified accessibility complaint (WCAG-blocking); **any** confirmed instance of an assistant citing a fit signal incorrectly; a model-library bias flag where a covered body-type cohort's satisfaction trails the mean by **> 10 pts**; or **any** consent / privacy issue. A red trigger reverts the sub-class to the always-on size-recommendation fallback within the rollback window and pages the named owner. Keep size-recommendation fallback always-on so a rollback doesn't degrade the PDP back to a 2024 experience.

9. **Vendor neutrality and exit plan** — Whatever vendor or in-house pattern is used, require: (a) measurement data flows to the merchant's warehouse, not just the vendor's dashboard; (b) the merchant owns the fit profile and consent records; (c) point-of-measure dictionary is portable; (d) assistant-facing structured data sits on the merchant's domain; (e) a documented exit and migration path. Avoid lock-in on a vendor whose contract claims joint ownership of customer-fit data.

10. **Config-utilization checklist** — Confirm the rollout plan uses `categories_in_scope`, `regions`, `consent_regimes`, `target_assistants`, `fit_taxonomy`, `point_of_measure_dictionary`, and `accessibility_minima` from `config.yml` rather than generic placeholders.

**Output requirements:**

- **Eligibility tiering** — Sub-class × tier (Now / Next / Later / Skip) with the data, return-rate, and consent rationale per cell
- **Pattern-selection table** — Sub-class → recommended VTO pattern with one-line rationale
- **AI-surface fit signal spec** — Structured-data fields, fit-note derivation rule, and example schema for one Now-tier sub-class
- **Diversity and accessibility audit** — Model-library coverage, gaps, and graceful-fallback rule
- **Consent and data-retention plan** — Regime × capture surface × consent flow × retention window
- **Measurement plan** — Test design, primary metric, guardrails, statistical-power note
- **Inline-on-AI-surface eligibility table** — Assistant × eligibility status × blockers × fix-it owner
- **Rollout sequencing and rollback triggers**
- **Vendor neutrality / exit plan**
- **Config-utilization checklist**
- Professional formatting suitable for retail digital, merchandising, customer-experience, and legal stakeholders
- Correct fit-program terminology (point-of-measure, true-to-size, on-model generation, AR overlay, semantic body model, garment vs. body measurement, size-recommendation engine, BIPA / CUBI / HB1493, EU AI Act high-risk)
- Saved to `outputs/` if the user confirms

## Example Output

**Input (abbreviated):** *Northbourne* — mid-market apparel + footwear DTC on Shopify. In scope: women's denim, women's dresses, men's footwear, bras/activewear, men's tees, socks. Biometric jurisdictions in customer base: IL, TX, WA, CO, OR. Target assistants: Google Shopping (inline VTO), Perplexity Shop. Config present: `accessibility_minima`, `consent_regimes`, `target_assistants`. Trailing-12-mo fit-coded return rates supplied per sub-class.

---

**1. Eligibility tiering (scored per step 1)**

| Sub-class | Fit-return | (a) ≤40 | (b) ≤25 | (c) ≤20 | (d) ≤15 | Score | Tier |
|---|---:|---:|---:|---:|---:|---:|---|
| Women's denim | 24% | 40 | 25 (full POM) | 20 | 7 | **92** | Now |
| Men's footwear | 15% | 30 | 25 (last specs) | 20 | 7 | **82** | Now |
| Women's dresses | 19% | 38 | 12 (chart only) | 20 | 7 | **77** | Now |
| Bras / activewear | 22% | 40 | 12 | 0 (inconsistent) | 0 (no camera consent) | **52** | Next |
| Men's tees | 8% | 16 | 12 | 20 | 7 | 55 | Later |
| Socks | 3% | 6 | — | — | — | — | Skip |

Denim leads the Now rollout (highest score, highest fit-return). Bras/activewear has the need (22%) but is gated **Next** until camera-consent flow ships in IL/TX/WA/CO/OR and PDP imagery is standardized.

**2. Pattern selection (Now tier)**

| Sub-class | Pattern | Why |
|---|---|---|
| Women's denim | Garment-vs-body comparison | Size-chart confusion drives returns; compare to a pair the shopper already owns |
| Women's dresses | On-model generation | Shoppers want drape on a body type near theirs |
| Men's footwear | Semantic body model | Last shape / toe box / instep beats 2D imagery |
| *(all)* | Size-recommendation engine | Always-on fallback beneath every richer pattern |

**3. AI-surface fit-signal spec (denim, one SKU)**

```json
{ "additionalProperty": [
  { "name": "fitAdvice", "value": "runs-small", "sampleSize": 1840 },
  { "name": "shoulderFit", "value": "true-to-size" },
  { "name": "waistPOM_in", "value": "28–29 at size 28" } ],
  "sizeSystem": "US", "sizeGroup": "womens" }
```
Fit-note derivation: mine ≥ 500 fit-coded reviews per SKU; publish a signal only above that sample floor. Pair with the `agentic-commerce-readiness` AEO/GEO layer so an assistant answering "does this run small?" cites Northbourne's own data (1,840-review basis) instead of third-party reviews.

**4. Diversity & accessibility audit:** on-model library covers 4 body types / 5 skin tones; **gap** — no adaptive/seated representation and no > size-18 dress model. Graceful fallback wired: any shopper the library doesn't cover sees "your body type isn't in our library yet — here's our size-chart predictor" rather than a mismatched render. Diversity targets logged as auditable, not aspirational.

**5. Consent & retention:** denim/dresses use garment-comparison + size-recommendation only — **no biometric capture**, so no opt-in gate. Bras/activewear camera capture (when it ships) requires IL BIPA / TX CUBI / WA HB1493 / CO / OR opt-in, 12-mo retention max, one-tap deletion routed to the existing CCPA/GDPR DSR workflow, proof-of-consent stored. Under-18: camera gated behind parental consent.

**6. Measurement plan:** SKU-stratified holdout — half of Now-tier denim SKUs get VTO, matched-comparable denim SKUs stay pre-VTO. Pre-registered primary metric: **fit-coded return rate within 60 days of purchase.** Guardrails: conversion, AOV, refund-vs-exchange split, CS contact rate, accessibility-complaint rate. Power note: at a 24% base fit-return rate, detecting a 3-pt absolute reduction (24%→21%) at 80% power / α=0.05 (two-sided) needs ≈ 3,000 orders per cell.

**7. Inline-on-AI-surface eligibility**

| Assistant | Status | Blocker / owner |
|---|---|---|
| Google Shopping (inline VTO, post-Apr 30 2026) | Eligible after feed fix | Needs ≥ 3 images/SKU + GTIN accuracy — Merch |
| Perplexity Shop | Blocked | Agent-readable PDP fit signals (step 3) not yet live — Web eng |

**8. Rollout & rollback:** start denim × Google Shopping to prove the loop, then footwear, then dresses; 2-week soak each. Rollback if VTO-complaint rate > 2% (7-day), any WCAG-blocking accessibility complaint, any confirmed assistant mis-cite, a cohort satisfaction gap > 10 pts, or any consent issue — reverting that sub-class to the always-on size-recommendation fallback.

**9. Vendor neutrality:** measurement data lands in Northbourne's warehouse (not vendor-only); merchant owns fit profiles + consent records; POM dictionary portable; assistant-facing structured data on `northbourne.com`; documented exit path — no joint-ownership clause on customer-fit data.

**Config-utilization checklist:** applied `accessibility_minima`, `consent_regimes`, `target_assistants`. Flagged for backfill: `point_of_measure_dictionary` partial (dresses chart-only), `fit_taxonomy` absent.
