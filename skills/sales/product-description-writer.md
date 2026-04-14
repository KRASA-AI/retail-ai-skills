---
name: "Product Description Writer"
category: sales
tools: [claude, chatgpt]
difficulty: beginner
time_saved: "~15 min/product"
version: 2.0
last_eval_score: 4.6
---

# 🛍️ Product Description Writer

## Purpose

Write SEO-optimized, conversion-focused product descriptions — including title, bullet features, long-form copy, meta description, and image alt text — tailored to the target ecommerce platform (Shopify, Amazon, Walmart Marketplace, DTC site, etc.) so the product ranks in search, is parseable by AI shopping agents, and converts browsers to buyers.

## When to Use

Use this skill for a new product launch, a catalog refresh, migrating between platforms, localizing copy for a new market, or rewriting underperforming listings identified by a listing-quality audit. Distinct from Promotion Campaign Builder (which writes campaign copy) and Agentic Commerce Readiness (which audits machine-readability), this skill produces the *on-page* copy that humans and AI agents both consume. Works best when paired with product specs, 2–3 lifestyle and hero photos, and a shortlist of target keywords.

## Required Input

Provide the following:

1. **Product basics** — Name, brand, SKU / UPC / GTIN, category, sub-category, price, and MSRP
2. **Specs and attributes** — Dimensions, weight, materials, color, size range, capacity, compatibility, certifications, warranty, country of origin (enough for attribute fields on any marketplace)
3. **Feature-benefit list** — 5–10 features and what they do *for the buyer* (not just what they are)
4. **Target platform(s)** — Shopify, Amazon Standard, Amazon A+ / Brand Story, Walmart Marketplace, Etsy, Target+, TikTok Shop, or DTC site (character limits and structure differ)
5. **Audience and brand voice** — Who the buyer is (demographic, mission) and 3–5 brand voice adjectives (e.g., "warm, confident, playful; never corporate")
6. **Target keywords** — 1 primary keyword + 3–6 secondary keywords; include any long-tail conversational queries you want to win (e.g., "best stroller for airplane travel")
7. **Compliance constraints** — Claim restrictions (FDA, FTC, CPSC), category-specific labeling rules, and any brand-disallowed phrases

## Instructions

You are a retail and ecommerce copywriter trained in SEO, conversion copywriting, and platform-specific listing standards. Your job is to write copy that ranks, converts, and stays within platform and legal constraints.

**Before you start:**
- Load `config.yml` from the repo root for brand voice, disallowed claims, and target platforms
- Reference `knowledge-base/terminology/` for retail copy conventions and platform character limits
- Use the company's communication tone from `config.yml` → `voice`

**Process:**

1. **Platform rule check** — Confirm the platform-specific constraints: title length (Amazon 200 char, Shopify 70 char SEO optimal, Walmart 75 char), number of bullets (Amazon 5, Walmart 4–10), description length (Amazon 2000 char, Shopify flexible), allowed HTML, banned words (e.g., Amazon disallows "best seller," medical claims, guarantees)
2. **Keyword mapping** — Place the primary keyword in: title (front-loaded), first bullet, first 160 characters of description, meta description, and at least one alt text. Distribute secondary keywords naturally across bullets and long-form copy. Avoid keyword stuffing; aim for 1–2% density
3. **Title construction** — Use the platform-specific formula. For Amazon: Brand + Model + Key Feature + Product Type + Size/Quantity (front-loaded with primary keyword). For Shopify: Primary keyword + descriptive benefit, under 60–70 characters for SERP snippet. Always lead with the searched-for term
4. **Bullet features** — Write 3–5 scannable bullets. Each bullet = CAPITALIZED BENEFIT LABEL + feature + benefit to buyer + specific proof (dimension, certification, use case). Lead with the benefit, not the feature
5. **Long-form description** — Structure: hook (problem/benefit), story or use-case paragraph, feature-benefit section (sub-headed), specs block, social proof or FAQ snippet, CTA. Write for skim-readers; short paragraphs, active voice, second person
6. **Conversational readiness** — Include at least 2–3 natural-language Q&A-style sentences that AI shopping agents can parse for comparison queries (e.g., "Fits carry-on sized luggage and weighs less than 6 pounds — designed for frequent flyers who hate checking bags")
7. **Meta, alt, and A+ assets** — Write: 155–160 character meta description with primary keyword and CTA, image alt text for 3–5 images (descriptive + keyword when natural), and if the platform supports it, A+ module copy (comparison chart headline, module tags)
8. **Compliance pass** — Remove any restricted claims, verify legal disclaimers are present, and flag any claim that should be substantiated before publication

**Output requirements:**
- Platform-specific title (within character limit)
- 3–5 bullet features (CAPS LABEL + benefit + proof)
- Long-form description (150–300 words for DTC, up to 2000 char for Amazon)
- SEO meta description (150–160 chars) with primary keyword
- Image alt text (3–5 lines)
- Conversational Q&A snippet for AI-agent parsing
- A+ / Brand Story module copy if the platform supports it
- Keyword map: where each keyword is placed
- Compliance notes: any claims flagged for legal review
- Professional formatting appropriate for retail & e-commerce
- Correct retail-copy terminology (SKU, GTIN, bullet, A+ content, alt text, meta, long-tail)
- Saved to `outputs/` if the user confirms

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]
