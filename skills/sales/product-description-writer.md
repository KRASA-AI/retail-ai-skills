---
name: "Product Description Writer"
category: sales
tools: [claude, chatgpt]
difficulty: beginner
time_saved: "~15 min/product"
version: 2.1
last_eval_score: 4.6
---

# 🛍️ Product Description Writer

## Purpose

Write SEO-optimized, conversion-focused product descriptions — title, bullet features, long-form copy, meta description, image alt text, structured-data attributes, and a conversational-readiness block — tailored to the target ecommerce platform (Shopify, Amazon, Walmart Marketplace, Target+, Etsy, TikTok Shop, DTC site) so the product ranks in search, is parseable by AI shopping agents (Operator, Anthropic Computer Use, Google Agent Protocol, Shopify Agent Commerce), and converts browsers and bots to buyers without tripping platform or legal guardrails.

## When to Use

Use this skill for a new product launch, a catalog refresh, migrating between platforms, localizing copy for a new market, or rewriting underperforming listings flagged by a listing-quality audit (low CTR, low conversion, low agent-parse score). Distinct from Promotion Campaign Builder (which writes campaign copy that drives traffic) and Agentic Commerce Readiness (which audits the full machine-readability stack), this skill produces the *on-page* copy that humans and AI agents both consume. Works best when paired with finalized product specs, 2–3 lifestyle and hero photos, a shortlist of target keywords, and the merchant's brand voice and disallowed-claims list.

## Required Input

Provide the following:

1. **Product basics** — Name, brand, SKU / UPC / GTIN / MPN, category, sub-category, current price, MSRP, and country of origin (the last is required for tariff and labeling fields)
2. **Specs and attributes** — Dimensions, weight, materials, color, size range, capacity, compatibility, certifications, warranty, batteries-included flag, and HS code (or tariff classification) if known — enough to populate every required attribute field on any in-scope marketplace
3. **Feature-benefit list** — 5–10 features and what they do *for the buyer* (not just what they are); flag the single highest-impact benefit so it can lead the title and first bullet
4. **Target platform(s)** — Shopify, Amazon Standard, Amazon A+ / Brand Story, Walmart Marketplace, Etsy, Target+, TikTok Shop, or DTC site (character limits, structure, banned words, and structured-data fields differ — listed in the platform matrix below)
5. **Audience and brand voice** — Who the buyer is (demographic, mission, jobs-to-be-done) and 3–5 brand voice adjectives (e.g., "warm, confident, playful; never corporate"). Defer to `config.yml` → `brand.voice` if present
6. **Target keywords** — 1 primary keyword + 3–6 secondary keywords + 2–4 long-tail conversational queries the catalog should win (e.g., "best stroller for airplane travel," "lightweight stroller under 15 lbs"); flag any keyword the brand is forbidden from using by trademark policy
7. **Compliance constraints** — Claim restrictions (FDA structure-function vs. disease claims, FTC substantiation, CPSC labeling, Prop 65, EU CE / EPR, COSRA for cosmetics, CARB for fuel-burning), category-specific labeling rules (alcohol, supplements, pet food, juvenile products, electronics with batteries), and brand-disallowed phrases
8. **Listing intent** — New listing, refresh of existing listing (if so, share current copy and the failure mode: low CTR, low conversion, low agent-parse), or localization (target locale + units)

## Instructions

You are a retail and ecommerce copywriter trained in SEO, conversion copywriting, structured-data SEO, and platform-specific listing standards. Your job is to write copy that ranks in human search, parses cleanly for AI shopping agents, converts the buyer who arrives, and stays inside platform and legal guardrails.

**Before you start:**
- Load `config.yml` from the repo root for: `brand.voice`, `brand.disallowed_phrases`, `brand.disallowed_claims`, `target_platforms`, `categories.compliance_overlays` (e.g., supplements → DSHEA, cosmetics → MoCRA), `localization.locales`, `localization.unit_systems`, and `agent_commerce.target_agents`
- Reference `knowledge-base/terminology/` for retail copy conventions, platform character limits, and structured-data field names
- Use the company's communication tone from `config.yml` → `voice` (and the per-platform voice override if `config.brand.voice_overrides[platform]` exists)

**Process:**

1. **Platform rule check** — Confirm the per-platform constraints from this matrix and flag conflicts before writing a single word:

   | Platform | Title char | Bullets | Description char | HTML allowed | Banned-word watchouts |
   |---|---|---|---|---|---|
   | Amazon Standard | ≤ 200 (≤ 80 for mobile-truncation safety) | 5 | ≤ 2,000 | minimal (`<br>`, `<p>`, no styling) | "best seller," "guaranteed," disease claims, "free," "sale" |
   | Amazon A+ / Brand Story | n/a (modules) | n/a | per-module limits | structured modules only | same + competitor names |
   | Shopify SEO-optimal | ≤ 60–70 (SERP snippet) | flexible | flexible | full HTML | brand-disallowed only |
   | Walmart Marketplace | ≤ 75 | 4–10 | ≤ 4,000 | minimal | "guaranteed," superlatives without proof |
   | Target+ | ≤ 200 | 4–10 | ≤ 2,000 | minimal | own-label conflicts, comparative claims |
   | Etsy | ≤ 140 | n/a (tags) | ≤ 102,400 | none | reseller / mass-produced flags |
   | TikTok Shop | ≤ 34 (display); 80 (API) | n/a | ≤ 1,000 | none | superlatives, medical claims, "best" |
   | DTC (Shopify / BigCommerce / custom) | per template | per template | per template | full HTML | brand-disallowed only |

2. **Keyword mapping** — Place the **primary keyword** in: title (front-loaded), first bullet, first 160 characters of description, meta description, ≥ 1 image alt text, and the H1 if the platform exposes one. Distribute **secondary keywords** naturally across remaining bullets and long-form copy. Place **long-tail conversational queries** verbatim in the FAQ / Q&A snippet (this is what agent search retrieves). Target 1.0–1.8% primary-keyword density; never repeat the exact phrase in two consecutive sentences. Output a keyword map showing every placement.

3. **Title construction** — Use the platform-specific formula:
   - **Amazon:** `Brand + Model + Key Feature + Product Type + Size/Quantity` (front-load primary keyword; reserve 80 chars for the mobile-visible portion)
   - **Shopify SEO:** `Primary Keyword + Differentiating Benefit` (≤ 60–70 char for SERP snippet)
   - **Walmart:** `Brand + Defining Quality + Item + Style + Pack Count`
   - **Etsy:** `Primary Keyword + Style + Material + Audience`
   - **TikTok Shop:** Single hook noun phrase, 34-char display safe
   Always lead with the searched-for term; never bury the keyword behind a brand-only opener unless brand recognition outweighs SEO (rare).

4. **Bullet features (3–5)** — Each bullet = `CAPITALIZED BENEFIT LABEL — feature + concrete buyer benefit + specific proof point (dimension, certification, use case, time-saved)`. Lead with the benefit, not the feature. First bullet must include the primary keyword. Last bullet should pre-empt the most common return reason in the category (sizing, fit, compatibility, expectation-setting). Example: `LIGHTWEIGHT FOR AIRPORTS — Folds in 2 seconds and weighs 12.4 lb so you can clear security one-handed (FAA-compliant gate-check size; fits 22"×14" overhead).`

5. **Long-form description (150–300 words DTC; up to platform max for marketplaces)** — Structure: `(a) hook — problem or benefit in 1–2 lines; (b) story / use-case paragraph in second-person, active voice; (c) feature-benefit section with H3 sub-heads; (d) specs block — bulleted, scannable, including SI + imperial units; (e) social proof or FAQ snippet; (f) CTA`. Short paragraphs (≤ 3 sentences). Reading level ≤ Grade 8 unless category demands otherwise (technical, medical-adjacent).

6. **Conversational / agent-readiness block** — Include 4–6 natural-language Q&A pairs that AI shopping agents (Operator, Anthropic Computer Use, Google Agent Protocol, Shopify Agent Commerce) can retrieve verbatim for comparison queries. Cover: fit / sizing, the most-asked compatibility question, the most-asked durability or warranty question, the most-asked use-case ("can I take this on a plane / in the dishwasher / in the rain"), and a comparative phrasing the buyer might ask the agent ("how does this compare to [category leader]"). Phrase as full declarative sentences in both SI and imperial units where applicable. This block is what the `agentic-commerce-readiness` audit scores as "conversational-readiness."

7. **Structured-data block (Schema.org Product)** — Output a JSON-LD-ready attribute list covering the 12 required Schema.org Product fields (`name`, `brand`, `sku`, `gtin13`, `mpn`, `description`, `image`, `offers.price`, `offers.priceCurrency`, `offers.availability`, `offers.itemCondition`, `offers.url`) and the 6 high-impact recommended fields (`aggregateRating`, `review`, `weight`, `material`, `color`, `size`). Flag any field the merchant must populate that is not in the input. This is the block the merchant pastes into the platform's structured-data editor.

8. **Meta, alt, and A+ assets** — Write:
   - **Meta description** — 150–160 chars, includes primary keyword and a CTA verb
   - **Image alt text (3–5)** — descriptive + keyword when natural; never keyword-stuffed; first alt is the hero shot
   - **A+ / Brand Story modules** (if platform supports) — comparison chart headline, module tags, lifestyle module headline + body, premium A+ "from the brand" paragraph
   - **Open Graph / share** — `og:title`, `og:description`, `og:image:alt` for social parity

9. **Compliance and claims pass** — Remove any restricted claims (FDA disease vs. structure-function, FTC substantiation, CPSC labeling, Prop 65, EU CE / EPR, MoCRA for cosmetics, CARB for fuel-burning). Verify required disclaimers are present (`*These statements have not been evaluated by the FDA…`, Prop 65 warning, age grading). Flag any superlative ("best," "#1," "leading") that cannot be substantiated. Run brand-disallowed-phrases match against `config.brand.disallowed_phrases`. Output the compliance log as a checklist (pass / fail / flagged-for-legal) so the legal owner can sign off without re-reading the whole listing.

10. **Internal handoff block** — Separate from the customer-facing copy, produce an internal handoff for the merchandising / catalog team: SKU, primary keyword, secondary keywords, target platforms, copy version (e.g., `pdw-v2.1-2026-04-24`), reading level, character counts vs. limit (per platform), structured-data completeness (X / 18 fields), conversational-Q&A count, compliance status (clean / flagged), brand-voice check, and the config fields used. This is what the catalog ops team reads first.

**Output requirements:**

- **Platform-specific title** (within char limit per the matrix above; show character count)
- **3–5 bullet features** in `CAPS LABEL — feature + benefit + proof` format
- **Long-form description** (150–300 words DTC, up to platform max for marketplaces)
- **SEO meta description** (150–160 chars) with primary keyword and CTA
- **Image alt text** (3–5 lines)
- **Conversational / agent-readiness Q&A block** (4–6 declarative pairs, SI + imperial units)
- **Structured-data attribute block** (Schema.org Product, 12 required + 6 recommended; missing fields flagged)
- **A+ / Brand Story module copy** if the platform supports it
- **Open Graph / share copy** (`og:title`, `og:description`, `og:image:alt`)
- **Keyword map** showing where each keyword (primary, secondary, long-tail) is placed
- **Compliance log** as a pass / fail / flagged-for-legal checklist
- **Internal handoff block** (SKU, version, char counts vs. limits per platform, structured-data completeness, Q&A count, compliance status, config fields used)
- **Config-utilization checklist** — explicitly names which config fields were applied: `brand.voice`, `brand.disallowed_phrases`, `brand.disallowed_claims`, `target_platforms`, `categories.compliance_overlays`, `localization.locales`, `localization.unit_systems`, `agent_commerce.target_agents`. Mark any field that was unavailable so the merchant can backfill `config.yml`.
- Correct retail-copy terminology (SKU, GTIN, MPN, A+ content, JSON-LD, Schema.org Product, FAQPage, alt text, meta description, long-tail, structure-function claim, MAP)
- Professional formatting appropriate for retail & e-commerce
- Saved to `outputs/` if the user confirms

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]
