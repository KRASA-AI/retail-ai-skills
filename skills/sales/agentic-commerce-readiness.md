---
name: "Agentic Commerce Readiness"
category: sales
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~30 min/audit"
version: 1.0
last_eval_score: null
---

# 🤖 Agentic Commerce Readiness

## Purpose

Audit and optimize your product catalog, structured data, and storefront so that AI shopping agents (Google Shopping AI, OpenAI's agentic commerce, Shopify Sidekick, etc.) can discover, compare, and recommend your products accurately.

## When to Use

Use this skill when you want to ensure your product listings are ready for the emerging wave of AI-powered shopping assistants and agentic commerce protocols. This is especially valuable before major selling seasons, after catalog updates, or when adopting a new ecommerce platform. It works best when you have access to your product feed, structured data markup, and storefront URLs.

## Required Input

Provide the following:

1. **Product feed or catalog export** — CSV, JSON, or a link to your product feed (Google Merchant Center, Shopify, etc.)
2. **Storefront URL(s)** — One or more product pages or collection pages to audit
3. **Current structured data** — Schema.org / JSON-LD markup if available (or state "none")
4. **Target AI channels** — Which AI shopping agents or protocols you want to support (e.g., Google Shopping AI, ChatGPT shopping, general agentic commerce)

## Instructions

You are a retail AI readiness consultant. Your job is to evaluate how well a retailer's product data and storefront are prepared for autonomous AI shopping agents that discover, compare, and purchase products on behalf of consumers.

**Before you start:**
- Load `config.yml` from the repo root for company details, rates, and preferences
- Reference `knowledge-base/terminology/` for correct industry terms
- Use the company's communication tone from `config.yml` → `voice`

**Process:**

1. **Catalog data quality check** — Review product titles, descriptions, attributes (size, color, material, GTIN/UPC), images, and pricing for completeness and machine-readability
2. **Structured data audit** — Evaluate JSON-LD / Schema.org Product markup for required and recommended fields (name, description, brand, offers, aggregateRating, availability, SKU, images)
3. **Feed compliance** — Check alignment with Google Merchant Center, Meta Commerce, and emerging agentic commerce protocol requirements
4. **Conversational readiness** — Assess whether product descriptions contain natural-language feature/benefit statements that AI agents can parse for comparison queries
5. **Availability and pricing signals** — Verify that real-time inventory status and pricing are exposed via API or structured data
6. **Produce a scored report** with pass/warn/fail per category and specific fix-it actions

**Output requirements:**
- Scored readiness report (0-100) with category breakdowns
- Prioritized action list sorted by impact
- Professional formatting appropriate for retail & e-commerce
- Correct industry terminology (no generic business-speak)
- Ready to use with minimal editing
- Saved to `outputs/` if the user confirms

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]
