---
name: "Virtual Try-On & Fit Confidence Audit"
category: sales
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~45 min/audit"
version: 1.0
last_eval_score: null
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

1. **Eligibility tiering by sub-class** — Score each catalog sub-class on (a) return-rate sensitivity to fit, (b) availability of point-of-measure data, (c) image consistency for on-model generation, and (d) consent-regime coverage. Tier into Now (high-return + good data), Next (high-return but data gaps), Later (lower-return), and Skip (categories where VTO won't move the needle — e.g., commodity socks, hardware adjacent SKUs). Build the rollout sequence from the Now tier first.

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

8. **Rollout sequencing and rollback triggers** — Sequence by Now-tier sub-class, capacity of the implementing team, and platform readiness. Start with one sub-class × one assistant surface to prove the measurement loop, then fan out. Define rollback triggers: customer-complaint rate from the VTO experience > X%, accessibility complaint, an assistant cites a fit signal incorrectly, a model-library bias flag, or a consent / privacy issue. Keep size-recommendation fallback always-on so a rollback doesn't degrade the PDP back to a 2024 experience.

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

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]
