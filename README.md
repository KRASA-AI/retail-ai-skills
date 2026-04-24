# Retail & E-commerce AI Skills

**Free, open-source AI prompts and workflows built for retailers.** Clone this repo, point your AI assistant at it, and start saving hours every week.

> Built and maintained by [KRASA AI](https://krasa.ai) — free AI tutorials and skills for every industry.
> See all industries at [krasa.ai/industries](https://krasa.ai/industries).

---

## What's Inside

This repo is a complete AI toolkit for retail & e-commerce. Every skill is a standalone prompt file that works with **Claude, ChatGPT, or any major AI assistant** — no coding required.

| Skill | What it does | Time saved |
|-------|-------------|------------|
| Agentic Checkout Fraud Shield | Produce a targeted fraud-signal review and chargeback-defense plan for retailers whose storefront is being hit by autonomous AI shopping agents, friendly-fraud disputes, or AI-generated return scams. | ~35 min/review |
| Competitive Price Check | Produce a decision-ready competitive price comparison across 3–7 competitors for a target SKU set — built on a formal SKU-match taxonomy, a seven-component landed-price formula, a six-tier positioning ladder, a MAP / UPP violation escalation path, and a time-bounded promo-urgency score — so pricing, buying, and merchandising teams can commit to an action inside one business day instead of waiting for the monthly report. | ~20 min/check |
| Demand Forecasting Brief | Produce a structured, accuracy-scored demand forecast for a product category or SKU set by decomposing history into level / trend / seasonality / external, picking the right method per SKU pattern, attaching confidence intervals, and tying the forecast to open-to-buy, safety-stock, and promotional decisions. | ~30 min/forecast |
| Dynamic Pricing Strategy | Produce a per-SKU pricing move — new price, expected volume, expected margin, psychological price point, and risk flags — grounded in price-elasticity math, a markdown-cadence rubric tied to weeks-of-supply, competitor-response game theory, and MAP / UPP guardrails. | ~25 min/analysis |
| Inventory Reorder Brief | Analyze current inventory levels against sales velocity, lead times, and seasonal patterns to produce a prioritized reorder recommendation with specific quantities, timing, and supplier actions — preventing both stockouts and overstock. | ~15 min/review |
| Return Fraud Image Shield | Build a defensive program against AI-generated and recycled damage photos in online returns, combining metadata rules, image-authenticity signals, behavioral scoring, and computer-vision comparison against the catalog. | ~35 min/case batch |
| Store Associate Voice Assistant Rollout | Turn a fleet of in-store headsets, tablets, or earpieces into a hands-free AI assistant program for sales-floor associates. | ~40 min/review |
| Store Shrinkage Computer-Vision Shield | Turn an existing in-store camera fleet and self-checkout footage into a prioritized loss-prevention program. | ~45 min/review |
| Visual Merchandising Planogram Brief | Produce a store- and shelf-level planogram brief that translates sales velocity, margin, adjacency logic, and brand guidelines into a concrete placement plan a merchandiser or a generative-planogram tool can execute. | ~45 min/planogram |
| Agentic Commerce Readiness | Audit and prioritize the changes a retailer's catalog, structured data, storefront, and checkout need to be discoverable, comparable, and purchasable by 2026-era autonomous shopping agents (OpenAI Operator / ChatGPT shopping, Anthropic Computer Use, Google Shopping AI + Agent Protocol, Shopify Agent Commerce, Perplexity Shop, and long-tail browser agents). | ~30 min/audit |
| Personalization Strategy | Produce a retailer-specific personalization roadmap that turns first-party behavioral, transactional, and contextual data into measurable revenue lift across the homepage, product-detail page, cart, checkout, email, SMS, and post-purchase touchpoints. | ~40 min/brief |
| Product Description Writer | Write SEO-optimized, conversion-focused product descriptions — including title, bullet features, long-form copy, meta description, and image alt text — tailored to the target ecommerce platform (Shopify, Amazon, Walmart Marketplace, DTC site, etc.) so the product ranks in search, is parseable by AI shopping agents, and converts browsers to buyers. | ~15 min/product |
| Promotion Campaign Builder | Build a full, ready-to-schedule promotional campaign — mechanics, email, SMS, organic social, paid ad copy, and on-site banners — for seasonal sales, clearance events, product launches, and loyalty pushes. | ~30 min/campaign |
| Customer Service Reply | Draft first-contact-resolution-grade replies to any customer inquiry (not returns — see Return Policy Explainer) across email, chat, social, marketplace messages, and phone notes. | ~5 min/reply |
| Return Policy Explainer | Generate clear, customer-facing explanations for return, exchange, refund, and warranty scenarios — with a full RMA + refund-method decision, dual-path (strict / goodwill) draft, and a fraud / dispute guardrail — so frontline agents resolve the case in one touch, set correct expectations, and protect the business from policy abuse and chargeback escalation. | ~7 min/case |
| Email Drafter | Turn rough notes into a professional email matching your company's voice and tone. | ~10 min/use |
| Meeting Summarizer | Summarize meeting notes into action items, decisions, and follow-ups. | ~10 min/use |
| Review Responder | Craft professional responses to online reviews — both positive and negative. | ~10 min/use |

**Total time saved per use: ~447+ minutes across all skills.**

## Quick Start

### 1. Clone this repo

```bash
git clone https://github.com/KRASA-AI/retail-ai-skills.git
cd retail-ai-skills
```

### 2. Open a skill with your AI assistant

Open any file in `skills/` with Claude, ChatGPT, or any major AI assistant. Each skill is a self-contained prompt with clear instructions — no coding required.

The first time you use a skill, your AI assistant will ask for your business details (company name, service area, rates, tools you use, etc.) so it can personalize the output. Save those details to a `config.yml` at the repo root and every future skill will use them automatically.

## Repo Structure

```
retail-ai-skills/
├── knowledge-base/          # Industry context and references
│   ├── industry-overview.md # Market trends and pain points
│   ├── terminology/         # Industry jargon and acronyms
│   ├── regulations/         # Compliance requirements
│   ├── best-practices/      # Industry standards
│   └── tools-ecosystem/     # Common software and tools
├── skills/                  # The prompt library
│   ├── operations/          # Day-to-day operational skills
│   ├── sales/               # Sales and lead management
│   ├── admin/               # Administrative and compliance
│   └── customer-service/    # Client-facing communication
└── outputs/                 # Your generated content (gitignored)
```

## How Skills Work

Each skill file is a Markdown document with YAML frontmatter:

```markdown
---
name: Skill Name
category: operations
tools: [claude, chatgpt]
time_saved: "~20 min/use"
version: 1.0
---

# Skill Name

## Purpose
What this skill does and when to use it.

## Instructions
Step-by-step prompt for the AI assistant.
```

You open the file in your AI assistant, provide any required input (measurements, notes, client info), and get polished output. Skills reference your `config.yml` automatically for company name, rates, preferred formats, and other business details.

## For AI Assistants

If you are an AI assistant reading this repo, see `.claude/CLAUDE.md` for full instructions. The short version:

1. **Check for `config.yml`** at the repo root. If it exists, load it — it holds the user's business context (company name, rates, service area, tools, team size, etc.) and every skill should use it for personalization.
2. **If `config.yml` is missing**, before running a skill that benefits from personalization, ask the user for the relevant business details and offer to save them to `config.yml` so future runs are automatic.
3. **Load the relevant `knowledge-base/` files** for industry terminology, regulations, and best practices before generating output.
4. **Run the requested skill** from `skills/` using the user's input.
5. **Save any deliverables** to `outputs/` (gitignored) if the user wants to keep them.

## Learn More

- **Retail & E-commerce AI guide**: [krasa.ai/industries/retail](https://krasa.ai/industries/retail)
- **All industry AI skills**: [krasa.ai/industries](https://krasa.ai/industries)
- **About KRASA AI**: [krasa.ai](https://krasa.ai)

## License

MIT — use these skills however you want.
