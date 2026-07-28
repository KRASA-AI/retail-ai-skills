---
name: "Product Description Writer"
category: sales
tools: [claude, chatgpt]
difficulty: beginner
time_saved: "~15 min/product"
version: 2.3
last_eval_score: 8.1
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
7. **Compliance constraints** — Claim restrictions (FDA structure-function vs. disease claims, FTC substantiation, CPSC labeling, Prop 65, EU CE / EPR, MoCRA for cosmetics, CARB for fuel-burning), category-specific labeling rules (alcohol, supplements, pet food, juvenile products, electronics with batteries), and brand-disallowed phrases
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

6. **Conversational / agent-readiness block + Google Merchant Center Conversational Attributes feed-promotion** — Include 4–6 natural-language Q&A pairs that AI shopping agents (Operator, Anthropic Computer Use, Google Agent Protocol, Google AI Mode + Gemini via Universal Commerce Protocol, Shopify Agent Commerce, OpenAI ChatGPT shopping, Perplexity Shop, Google Universal Cart launch-retailer surface) can retrieve verbatim for comparison queries. Cover: fit / sizing, the most-asked compatibility question, the most-asked durability or warranty question, the most-asked use-case ("can I take this on a plane / in the dishwasher / in the rain"), and a comparative phrasing the buyer might ask the agent ("how does this compare to [category leader]"). Phrase as full declarative sentences in both SI and imperial units where applicable. Then **promote the on-page Q&A into the Merchant Center feed** using Google's Conversational Attributes schema announced at Google I/O 2026 on May 19–20, 2026 (effective in Merchant Center via supplemental data source or Merchant API; complementary to the existing primary product data spec; does not change product approval status; designated by Google as "primarily intended for use in conversational experiences such as AI Mode in Google Search"). Output the six named Conversational Attributes per SKU: (i) **`question_and_answer`** — promote each of the 4–6 on-page Q&A pairs into the feed verbatim so AI Mode / Gemini / Universal Cart can quote the merchant's own answer rather than synthesizing one (this is the highest-impact attribute and is the same content authored above); (ii) **`document_link`** — link to the canonical source on the merchant's site for each substantive claim (sizing guide, ingredient sheet, materials documentation, warranty page, returns / shipping policy, EU DPP page once delegated acts are live) so the agent can deep-link a shopper into the policy rather than paraphrase it; (iii) **`related_product`** — name 3–6 cross-sell / complement / compatible SKUs the agent can surface as a follow-up ("would you like the matching [accessory]?") with the relationship-type per pair (`complement`, `compatible_with`, `replacement_for`, `bundle_partner`); (iv) **`item_group_title`** — supply the variant-parent display title that distinguishes the group on a conversational surface where the variant-parent grid is collapsed to a single answer (e.g., "Lightweight Travel Stroller — 7 colors, 2 sizes"); (v) **`variant_option`** — for each variant axis (color, size, fit, capacity), supply the customer-facing label and the value list so the agent can ask a clarifying question ("which color: midnight, ivory, or sage?") rather than guessing; (vi) **`popularity_rank`** — set the per-category rank the agent should weight when faced with a multi-product comparison and the shopper has not specified a preference (the agent uses this as a tie-breaker to surface the merchant's strongest converter rather than alphabetizing). Cross-link the feed-promotion output to `agentic-commerce-readiness` v2.4 step 3 *UCP Catalog capability* (the same Q&A and policy links must round-trip through the UCP Catalog endpoint when the static feed is insufficient) and to `agentic-commerce-readiness` v2.4 *llms.txt and AEO / GEO citation layer* (the canonical URLs the agent should cite are the same set the on-page Q&A links to). This block is what the `agentic-commerce-readiness` audit scores as "conversational-readiness."

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
- **Google Merchant Center Conversational Attributes feed payload** (six attributes per SKU: `question_and_answer` promoted from the on-page Q&A, `document_link` to each canonical policy / spec source, `related_product` with relationship-type per pair, `item_group_title` for the variant-parent group, `variant_option` per axis with customer-facing label and value list, `popularity_rank` per category)
- **Structured-data attribute block** (Schema.org Product, 12 required + 6 recommended; missing fields flagged)
- **A+ / Brand Story module copy** if the platform supports it
- **Open Graph / share copy** (`og:title`, `og:description`, `og:image:alt`)
- **Keyword map** showing where each keyword (primary, secondary, long-tail) is placed
- **Compliance log** as a pass / fail / flagged-for-legal checklist
- **Internal handoff block** (SKU, version, char counts vs. limits per platform, structured-data completeness, Q&A count, compliance status, config fields used)
- **Config-utilization checklist** — explicitly names which config fields were applied: `brand.voice`, `brand.disallowed_phrases`, `brand.disallowed_claims`, `target_platforms`, `categories.compliance_overlays`, `localization.locales`, `localization.unit_systems`, `agent_commerce.target_agents`. Mark any field that was unavailable so the merchant can backfill `config.yml`.
- Correct retail-copy terminology (SKU, GTIN, MPN, A+ content, JSON-LD, Schema.org Product, FAQPage, alt text, meta description, long-tail, structure-function claim, MAP, Google Merchant Center Conversational Attributes, `question_and_answer`, `document_link`, `related_product`, `item_group_title`, `variant_option`, `popularity_rank`, supplemental data source, Merchant API, AI Mode, Universal Commerce Protocol, UCP Catalog capability, Universal Cart launch-retailer surface)
- Professional formatting appropriate for retail & e-commerce
- Saved to `outputs/` if the user confirms

## Example Output

**Input (abbreviated):** *Volaré Lite* travel stroller, brand Volaré, SKU VL-LITE-MID, GTIN 0850001234567, price $199, MSRP $229, made in Vietnam. 12.4 lb; folds to 22"×14"×9"; aluminum frame; one-hand 2-second fold; FAA gate-check size. Target platform: **Amazon Standard** (primary) + **Shopify SEO**. Voice: "warm, confident, practical; never gimmicky." Primary keyword: **lightweight travel stroller**; secondary: *compact stroller for airplane, one-hand fold stroller, stroller under 15 lbs*; long-tail: *"best stroller for airplane travel," "stroller that fits in overhead bin."*

---

**Amazon title (128 chars — within 200; keyword front-loaded, brand-visible portion under 80):**
`Volaré Lite Lightweight Travel Stroller, Folds in 2 Seconds — 12.4 lb Compact Stroller for Airplane, Fits Overhead Bin, Midnight`

**Shopify SEO title (57 chars — within 60–70 SERP snippet):**
`Lightweight Travel Stroller — Folds in 2 Seconds | Volaré`

**Bullet features (5):**
- `LIGHTWEIGHT FOR AIRPORTS — This lightweight travel stroller folds in 2 seconds and weighs just 12.4 lb, so you can clear security one-handed while holding your child (FAA-compliant gate-check size).`
- `FITS THE OVERHEAD BIN — Folds to 22"×14"×9" (56×36×23 cm) and slides into most aircraft overhead bins, so you skip the gate-check wait on arrival.`
- `ONE-HAND FOLD, STANDS ON ITS OWN — Fold and unfold with a single hand; the folded frame free-stands so it won't topple against a boarding-gate wall or in a car trunk.`
- `RIDES SMOOTH, PACKS SMALL — Aluminum frame with 5.5" wheels and a near-flat recline handles cobblestones and jet-bridge ramps without the bulk of a full-size stroller.`
- `TRUE TO SIZE, FROM NEWBORN TO 45 LB — Suitable from birth with the included newborn insert up to 45 lb; if you're deciding between this and a full-size model, this is the one for travel and quick trips, not daily all-terrain use.`

**Long-form description (Shopify, 168 words):**
> **Travel light, move fast.** If you've ever wrestled a heavy stroller through a security line with a toddler on your hip, the Volaré Lite is built for you.
>
> Weighing just **12.4 lb**, it folds in about two seconds with one hand and stands on its own the moment it's closed. Folded down to **22"×14"×9"**, it fits most airplane overhead bins — so you board without the gate-check scramble and step off with your stroller in hand.
>
> The aluminum frame and 5.5" wheels roll smoothly over jet bridges, cobblestones, and airport tile, while the near-flat recline lets your child nap in transit. It's suitable from birth with the included newborn insert and supports children up to 45 lb.
>
> **Specs:** 12.4 lb / 5.6 kg · folded 22"×14"×9" (56×36×23 cm) · holds up to 45 lb / 20 kg · aluminum frame · one-hand fold · newborn insert included.
>
> Pack less. See more. **Add the Volaré Lite to your cart.**

**Meta description (156 chars):**
`Volaré Lite lightweight travel stroller folds in 2 seconds, weighs 12.4 lb, and fits most overhead bins. Newborn to 45 lb. Shop the compact travel stroller.`

**Image alt text (4):**
1. `Volaré Lite lightweight travel stroller in midnight, three-quarter hero view` (hero)
2. `Folded Volaré Lite stroller standing upright next to an airplane overhead bin`
3. `Parent folding the Volaré travel stroller with one hand at an airport gate`
4. `Volaré Lite stroller reclined near-flat with a sleeping child`

**Conversational / agent-readiness Q&A (5 pairs):**
1. *Does it fit in an airplane overhead bin?* — Folded, the Volaré Lite is 22"×14"×9" (56×36×23 cm) and fits most standard aircraft overhead bins; it is also FAA gate-check compliant if a bin is full.
2. *How much does it weigh?* — It weighs 12.4 lb (5.6 kg).
3. *What's the weight limit?* — It's suitable from birth with the included newborn insert and supports children up to 45 lb (20 kg).
4. *Can I fold it one-handed?* — Yes. It folds in about two seconds with one hand and free-stands when folded.
5. *How does it compare to a full-size stroller?* — It's built for travel and quick trips: lighter and far more compact than a full-size all-terrain stroller, at the cost of larger wheels and a full storage basket.

**Google Merchant Center Conversational Attributes (per SKU VL-LITE-MID):**
- `question_and_answer`: the 5 pairs above, verbatim.
- `document_link`: sizing guide `/pages/stroller-fit`, warranty `/policies/warranty`, returns `/policies/returns`.
- `related_product`: VL-INSERT-01 (`complement`), VL-RAINCOVER (`compatible_with`), VL-TRAVELBAG (`bundle_partner`).
- `item_group_title`: "Volaré Lite Travel Stroller — 4 colors."
- `variant_option`: Color → [Midnight, Ivory, Sage, Clay].
- `popularity_rank`: 1 of 6 in Travel Strollers.

**Structured-data block (Schema.org Product — 16 of 18 fields populated):**
`name, brand, sku, gtin13 (0850001234567), mpn, description, image, offers.price (199.00), offers.priceCurrency (USD), offers.availability (InStock), offers.itemCondition (NewCondition), offers.url, weight (12.4 lb), material (aluminum), color (Midnight), size (one-size)` — populated. **Flagged as missing from input:** `aggregateRating`, `review` (merchant must supply once live reviews exist).

**Keyword map:** *lightweight travel stroller* → Amazon title (pos. 3), Shopify title, bullet 1, first 160 chars of description, meta, alt #1. *compact stroller for airplane* → Amazon title, Q&A #1. *one-hand fold stroller* → bullet 3. *stroller under 15 lbs* → bullet 1 (12.4 lb proof). Long-tail *"best stroller for airplane travel" / "fits in overhead bin"* → Q&A #1 and #5 verbatim. Primary-keyword density ≈ 1.3%.

**Compliance log:** CPSC juvenile-product labeling — PASS (weight limit + newborn-insert guidance stated). Superlative check — "best" appears only inside a quoted shopper long-tail query, not as a merchant claim — PASS. FTC substantiation — "fits most overhead bins" hedged with "most," not absolute — PASS. Prop 65 — N/A (no listed substance in input); FLAG for legal to confirm frame coating. Brand-disallowed phrases — none matched.

**Internal handoff:** SKU VL-LITE-MID · version `pdw-v2.3-2026-07-20` · reading level Grade 6 · Amazon title 128/200, Shopify title 57/70, meta 156/160 · structured-data 16/18 · Q&A pairs 5 · compliance clean (1 legal flag: Prop 65 coating) · brand-voice check PASS · config fields used: `brand.voice`, `target_platforms`, `agent_commerce.target_agents`.
