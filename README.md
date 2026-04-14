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
| Competitive Price Check | Produce a structured competitive price comparison across 3–7 competitors for a target set of SKUs, surfacing landed-cost gaps, promotional pressure, and positioning risks — so pricing, buying, and merchandising teams can react within one business day instead of waiting on a monthly report. | ~20 min/check |
| Demand Forecasting Brief | Produce a structured demand forecast for a product category or set of SKUs by synthesizing historical sales data, seasonal patterns, market trends, and external signals to guide buying, inventory, and promotional decisions. | ~25 min/forecast |
| Dynamic Pricing Strategy | Generate data-driven pricing recommendations by analyzing demand signals, competitor positioning, inventory levels, and margin targets to optimize revenue and sell-through rates. | ~20 min/analysis |
| Inventory Reorder Brief | Analyze current inventory levels against sales velocity, lead times, and seasonal patterns to produce a prioritized reorder recommendation with specific quantities, timing, and supplier actions — preventing both stockouts and overstock. | ~15 min/review |
| Visual Merchandising Planogram Brief | Produce a store- and shelf-level planogram brief that translates sales velocity, margin, adjacency logic, and brand guidelines into a concrete placement plan a merchandiser or a generative-planogram tool can execute. | ~45 min/planogram |
| Agentic Commerce Readiness | Audit and optimize your product catalog, structured data, and storefront so that AI shopping agents (Google Shopping AI, OpenAI's agentic commerce, Shopify Sidekick, etc.) can discover, compare, and recommend your products accurately. | ~30 min/audit |
| Product Description Writer | Write SEO-optimized, conversion-focused product descriptions — including title, bullet features, long-form copy, meta description, and image alt text — tailored to the target ecommerce platform (Shopify, Amazon, Walmart Marketplace, DTC site, etc.) so the product ranks in search, is parseable by AI shopping agents, and converts browsers to buyers. | ~15 min/product |
| Promotion Campaign Builder | Build a full, ready-to-schedule promotional campaign — mechanics, email, SMS, organic social, paid ad copy, and on-site banners — for seasonal sales, clearance events, product launches, and loyalty pushes. | ~30 min/campaign |
| Customer Service Reply | Draft professional, empathetic responses to customer inquiries and complaints across all retail channels (email, chat, social, marketplace messages) that resolve the issue on first contact whenever possible, reducing response time and follow-up tickets. | ~5 min/reply |
| Return Policy Explainer | Generate clear, customer-facing explanations for return, exchange, and refund scenarios that reduce support tickets, set correct expectations, and protect the business from policy abuse — all while keeping the tone friendly and on-brand. | ~5 min/case |
| Email Drafter | Turn rough notes into a professional email matching your company's voice and tone. | ~10 min/use |
| Meeting Summarizer | Summarize meeting notes into action items, decisions, and follow-ups. | ~10 min/use |
| Review Responder | Craft professional responses to online reviews — both positive and negative. | ~10 min/use |

**Total time saved per use: ~275+ minutes across all skills.**

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
