---
name: "Agentic Merchant Admin Assistant"
category: operations
tools: [claude, chatgpt]
difficulty: advanced
time_saved: "~90 min/scoping + ongoing"
version: 1.1
last_eval_score: null
---

# 🛠️ Agentic Merchant Admin Assistant

## Purpose

Scope, configure, and govern a merchant-side agentic admin assistant — the native-platform or third-party agent that runs *inside the store's back office* and executes merchandising and operations work from natural-language instructions (catalog organization, product sorting and tagging, discount/promotion setup, campaign creation and optimization, theme/storefront edits, app-extension actions, and "respond to this trend" tasks). Output is an autonomy-and-guardrail packet: a task-by-task autonomy tier map (observe / suggest / execute-with-approval / autonomous-within-budget), the dollar-and-scope ceilings each tier may act under, a human-gate and audit-log specification, a brand-voice and promotion-rule reconciliation against every other agentic surface, and a rollback / kill-switch plan. This skill produces a governed operating model for the merchant-admin agent, **not** a consumer-facing storefront audit and **not** the agent's persona copy — it decides what the assistant is allowed to do on its own and what it must hand back to a human.

## When to Use

Use this skill when (a) the merchant is turning on a native-platform merchant-admin agent — Shopify Sidekick in its proactive form (Sidekick everywhere across the admin by typing or voice, Campaign Autopilot, AI sales associate, Sidekick App Extensions, Sidekick Pulse), Shoplazza Athena (back-office product / order / discount / logistics / data-analysis via natural language), or Salesforce Agentforce Commerce Merchant Agent (organize catalogs, sort products, respond to trends in plain language, including headless back-end operation) — and leadership wants a documented autonomy boundary before the agent touches the live store, (b) an autonomous campaign or pricing agent (e.g., Campaign Autopilot) is about to create or optimize promotions without a per-action human click and the merchant needs a budget ceiling, an approval gate, and a brand-voice guardrail around it, (c) the admin agent's actions are colliding with another agentic surface — a price or promotion the admin agent sets diverges from what the consumer-facing storefront agent surfaces, or its campaign copy drifts from the authored brand persona, (d) an app-extension agent (Klaviyo, Loop, Smile, Judge.me, Yotpo, and similar partners acting through the platform's admin agent) can now execute multi-step tasks and the merchant needs to know which extension actions are auto-allowed vs. gated, or (e) an audit, finance, or legal stakeholder is asking "what can this thing do without us, what did it actually do, and how do we stop it." Distinct from `sales/agentic-commerce-readiness` (the *consumer-facing* storefront / catalog / checkout surface that outside shopping agents traverse), `sales/brand-agent-authoring` (the agent's *persona and voice*), `operations/dynamic-pricing-strategy` and `sales/promotion-campaign-builder` (the merchandising *decisions* the agent might execute), and `operations/labor-scheduling-agent` (a single-domain operations agent): this skill is the *cross-domain autonomy-governance layer* for the assistant that sits on the merchant's side of the glass.

## Required Input

Provide the following:

1. **Platform and agent surface** — Which merchant-admin agent is in scope (Shopify Sidekick + named sub-capabilities; Shoplazza Athena; Salesforce Agentforce Commerce Merchant Agent / headless; another native or third-party admin agent), the admin areas it can reach (catalog/PIM, pricing, promotions/discounts, campaigns/marketing, theme/storefront code, orders, inventory, customers, analytics, app extensions), and whether it operates by chat, voice, scheduled job, or proactive/autonomous trigger
2. **Task inventory** — The concrete back-office tasks the merchant wants the agent to handle, each tagged with its blast radius (reversible vs. hard-to-undo, customer-visible vs. internal, money-moving vs. not). Examples: retag a category, merge duplicate SKUs, draft vs. publish a discount, launch vs. optimize a campaign, edit live theme code, bulk-edit prices, issue a refund, change inventory thresholds
3. **Autonomy appetite and ceilings** — For each task class, how far the merchant is willing to let the agent act without a human: dollar ceiling per action and per day (discount depth, ad spend, refund amount), SKU/order count ceiling per bulk action, customer-visibility gate (nothing customer-facing publishes without review vs. low-risk auto-publish), and which actions are *never* autonomous regardless (price changes above X%, regulated-category edits, anything touching tax/legal copy)
4. **Brand-voice and promotion source-of-truth** — The authored persona from `brand-agent-authoring`, the promotion/margin guardrails from `promotion-campaign-builder` and `dynamic-pricing-strategy`, and the single named `voice_owner` from `config.yml` accountable for brand-voice reconciliation across every agentic surface (this skill included). State where the canonical version of each rule lives so the agent reads from one source rather than improvising
5. **App-extension and integration map** — Which partner extensions act *through* the admin agent (email/SMS, loyalty, reviews, returns, subscriptions), what each extension is permitted to do autonomously, and how their actions are reconciled so two agents do not both move the same lever (e.g., the admin agent and a loyalty extension both editing a promotion)
6. **Approval, audit, and identity** — Who approves a gated action (role, not person where possible), where the audit log lives and what each entry must capture (instruction, agent action, before/after state, dollar impact, approver, timestamp), how agent actions are attributed distinctly from human-staff actions in the platform's activity log, and least-privilege scoping of the agent's credentials
7. **Reconciliation with the consumer surface** — The relevant outputs of `agentic-commerce-readiness` (per-channel catalog syndication posture, pricing/promotion that outside shopping agents see, the `voice_owner`) so a change the *admin* agent makes internally stays consistent with what the *storefront* agent exposes externally
8. **Rollback and kill-switch posture** — How a bad autonomous action is reverted (version history, draft-vs-publish, transaction reversal), the threshold that pauses the agent automatically (error rate, dollar overrun, customer-complaint spike, brand-voice incident), and who holds the manual kill switch

## Instructions

You are a governance architect for a retailer's merchant-side agentic admin assistant. Your job is to let the agent absorb real back-office toil while making sure it never publishes a customer-visible change, moves money, or alters pricing/promotions/legal copy outside an explicitly authorized scope without the right human gate and an audit trail. Never recommend an autonomy tier that lets the agent execute an irreversible, customer-visible, or money-moving action without either a logged human approval or a hard, pre-authorized ceiling. Never let the agent author brand-voice or promotion rules from scratch when a canonical source already exists — it reads from `brand-agent-authoring` and the promotion/pricing guardrails, it does not reinvent them. Never collapse this internal-admin governance into the consumer-facing readiness audit; the two are reconciled, not merged.

**Before you start:**

- Load `config.yml` from the repo root for: `brand.voice`, `voice_owner`, `promotion_guardrails`, `pricing_guardrails`, `regulated_categories`, `approval_roles`, `audit_log_target`, and `kill_switch_owner`
- Reference `knowledge-base/terminology/` for merchant-admin vocabulary (autonomy tier, human-in-the-loop / human-on-the-loop, blast radius, least privilege, draft-vs-publish gate, agent attribution, app extension, Campaign Autopilot, headless commerce, kill switch, action ceiling, representment of an agent action)
- Pull the `voice_owner` and the promotion/pricing guardrails as read-only inputs — this skill governs *how* the agent applies them, it does not redefine them
- Use the merchant's communication tone from `config.yml → voice` for the rationale text leadership and store/merch ops will read

**Process:**

1. **Frame the toil being absorbed** — For each task class in the inventory, state the work the agent removes (hours/week, who does it today) and the failure cost if the agent gets it wrong (customer-visible error, margin leak, brand-voice incident, compliance exposure). This makes the autonomy decision a cost-of-error decision, not a capability list. Flag any task where the failure cost dwarfs the toil saved — those default to suggest-only regardless of platform capability.

2. **Blast-radius classification** — Tag every task on three axes: reversible vs. hard-to-undo, internal vs. customer-visible, money-moving vs. not. The combination drives the default autonomy ceiling. Reversible + internal + non-money (retag a draft category, propose a sort order) is a candidate for higher autonomy; hard-to-undo + customer-visible + money-moving (publish a sitewide discount, bulk-edit live prices, issue refunds) is human-gated or hard-ceilinged by default.

3. **Autonomy-tier map** — Assign each task class to a tier and bind the tier to a ceiling:
   - **Observe** — the agent reads and reports only; no writes.
   - **Suggest** — the agent drafts the change (a discount, a campaign, a retag, a theme edit) and leaves it in a draft/preview state for a named human to publish. Customer-visible changes default no higher than this unless a hard ceiling applies.
   - **Execute-with-approval** — the agent prepares the action and fires it only after a logged approval from an `approval_role`. The packet specifies the approval SLA and what the approver sees (instruction, before/after, dollar impact).
   - **Autonomous-within-budget** — the agent acts without a per-action click, bounded by a pre-authorized dollar ceiling per action and per day, a SKU/order-count ceiling, and a scope fence (which categories, which channels). Reserve for reversible, well-bounded work (e.g., Campaign Autopilot optimizing bids inside a fixed budget; auto-merging obvious duplicate SKUs). Every autonomous action still writes an audit entry.
   For each tier, state the explicit *never-autonomous* exclusions: price moves beyond the `pricing_guardrails` band, regulated-category edits, tax/legal/returns-policy copy, anything that changes the consumer-facing price or promotion an outside shopping agent would see without reconciliation in step 6.

4. **Approval gates and least-privilege scoping** — For every Execute-with-approval and Autonomous-within-budget task, specify the approval role (not a single named person where a role works), the approval SLA, and the credential scope the agent holds (least privilege — the agent's token grants only the admin areas in scope, not blanket admin). Recommend the agent operate under its own attributed identity so its actions are distinguishable from human-staff actions in the platform activity log and can be audited and, if needed, reverted as a set.

5. **Audit-log specification** — Define the mandatory fields per agent action: triggering instruction, agent's interpreted action, before/after state (or a link to version history), dollar impact, autonomy tier exercised, approver (if any), timestamp, and reconciliation status against the consumer surface. State where the log lives (`audit_log_target`) and the retention/review cadence. The log is the representment evidence if an autonomous action is later disputed internally or by a customer.

6. **Cross-surface reconciliation** — Reconcile the admin agent's authority against the other agentic surfaces so the merchant speaks with one voice and one price:
   - **Brand voice** — campaign copy, product descriptions, and sales-assistant replies the admin agent emits are reconciled against the `brand-agent-authoring` persona under the single `voice_owner`; the agent does not mint a second voice.
   - **Promotion/pricing** — any discount, price, or promotion the admin agent sets is checked against `promotion_guardrails` / `pricing_guardrails` *and* against what `agentic-commerce-readiness` exposes to outside shopping agents and syndicated catalogs, so an internally-set promo and the externally-visible price never diverge.
   - **App-extension de-confliction** — where the admin agent and a partner extension can both move the same lever (a loyalty extension and the admin agent both editing a promotion), assign a single owner per lever and a precedence rule so two agents do not race.

7. **Autonomous-campaign guardrail** — For an autonomous campaign/optimization agent (Campaign Autopilot-class), specify the fixed budget envelope, the objective and guardrail metrics (don't optimize conversion by eroding margin below the `promotion_guardrails` floor), the brand-voice gate on generated creative, the channels in scope, and the human review cadence (review the agent's choices on a schedule even when each action is within budget — human-*on*-the-loop, not just human-*in*-the-loop).

8. **Rollback and kill switch** — For each autonomy tier, specify how a bad action is reverted (draft discard, theme version restore, price/inventory rollback, transaction reversal) and the time window in which rollback is clean vs. customer-visible. Define the automatic pause triggers (error rate, dollar overrun vs. ceiling, customer-complaint or chargeback spike, a brand-voice incident) that drop the agent back to Suggest-only, and name the `kill_switch_owner` who can halt the agent entirely. The kill switch is a named human with a documented procedure, not an aspiration.

9. **Rollout staging** — Recommend a staged ramp: start the agent in Observe/Suggest on a low-blast-radius task class, promote a task to a higher tier only after a clean audit window, and never enable Autonomous-within-budget on a customer-visible money-moving task in the first phase. Include the metrics that justify promoting a task (approval-acceptance rate, error rate, dollar variance, brand-voice flags) and the metrics that demote it.

10. **Config-utilization checklist** — Confirm the packet uses `voice_owner`, `promotion_guardrails`, `pricing_guardrails`, `regulated_categories`, `approval_roles`, `audit_log_target`, and `kill_switch_owner` from `config.yml` rather than generic placeholders, and that brand-voice and promotion rules are cited as *read from* their canonical source, not redefined here.

**Output requirements:**

- **Autonomy-tier map** — Task class × tier (observe / suggest / execute-with-approval / autonomous-within-budget) × ceiling ($/action, $/day, count, scope fence) × never-autonomous exclusions
- **Blast-radius table** — Each task tagged reversible/customer-visible/money-moving with the resulting default ceiling
- **Approval and identity spec** — Approval role + SLA per gated task, least-privilege credential scope, agent-attribution requirement
- **Audit-log field spec** — Mandatory fields, log target, retention/review cadence
- **Cross-surface reconciliation note** — Brand-voice owner, promotion/pricing consistency with the consumer surface, app-extension lever ownership and precedence
- **Autonomous-campaign guardrail** — Budget envelope, guardrail metrics, brand-voice gate, review cadence
- **Rollback / kill-switch plan** — Per-tier reversal method + window, automatic pause triggers, named kill-switch owner
- **Staged rollout plan** — Ramp sequence with promote/demote metrics
- **Config-utilization checklist**
- **Professional formatting** appropriate for a merch-ops / e-commerce director plus finance, legal, and platform-admin stakeholders
- **Correct merchant-admin and governance terminology** (autonomy tier, blast radius, human-in-the-loop / human-on-the-loop, least privilege, agent attribution, draft-vs-publish gate, action ceiling, Campaign Autopilot, app extension, headless commerce, kill switch)
- Saved to `outputs/` if the user confirms

## Example Output

> Reference run. **Input:** Mid-market Shopify Plus apparel merchant turning on **Shopify Sidekick** (proactive, everywhere in admin) + **Campaign Autopilot**. In-scope admin areas: catalog/PIM, promotions/discounts, campaigns/marketing, theme, inventory thresholds. `voice_owner` = *Dana R. (Brand Director)*; `pricing_guardrails` GM floor 55%, max autonomous discount 15%; `regulated_categories` = none; `approval_roles` = {promo: Merch Manager, theme: Web Lead, refund: CX Supervisor}; `audit_log_target` = Shopify admin activity log + BigQuery export; `kill_switch_owner` = *Priya S. (Head of E-comm)*.

**Toil vs cost-of-error frame (excerpt):** *Merge duplicate SKUs* removes ~4 hrs/wk of catalog cleanup; failure cost is low (reversible, internal) → autonomy candidate. *Bulk-edit live prices* removes ~2 hrs/wk but failure cost is severe (customer-visible, money-moving, margin leak, diverges from what outside shopping agents see) → **defaults to gated regardless of platform capability**.

**Autonomy-tier map:**

| Task class | Blast radius (rev / cust-vis / money) | Tier | Ceiling / scope fence | Never-autonomous exclusion |
|---|---|---|---|---|
| Read/report catalog & sales | rev / int / no | **Observe** | read-only | — |
| Retag draft category | rev / int / no | **Autonomous-within-budget** | ≤ 200 SKUs/action, draft namespace only | published taxonomy |
| Merge obvious duplicate SKUs | rev / int / no | **Autonomous-within-budget** | ≤ 25 merges/day, high-confidence only | ambiguous matches → Suggest |
| Draft a discount | rev / cust-vis / no | **Suggest** | draft/preview only | auto-publish |
| Publish a discount | hard / cust-vis / money | **Execute-with-approval** | ≤ 15% depth, category-scoped; approver = Merch Manager, SLA 4 hrs | > 15% depth; sitewide |
| Campaign Autopilot bid opt. | rev / cust-vis / money | **Autonomous-within-budget** | **$500/day → $3,500/wk** envelope; GM floor 55% guardrail; channels: Meta+Google only | creative that breaks brand voice |
| Edit live theme code | hard / cust-vis / no | **Execute-with-approval** | version-pinned; approver = Web Lead | direct push to `main` theme |
| Bulk-edit live prices | hard / cust-vis / money | **Suggest** (hard-gated) | proposal only → human publish | any autonomous tier |
| Issue refund | hard / cust-vis / money | **Execute-with-approval** | approver = CX Supervisor | any autonomous tier |
| Change inventory reorder threshold | rev / int / no | **Suggest** | draft → planner accept | auto-apply |

**Autonomous-campaign guardrail (Campaign Autopilot):** budget envelope **$500/day = $3,500/week**; objective = ROAS, guardrail = *do not optimize conversion by discounting below the 55% GM floor*; brand-voice gate on all generated creative (reconciled to Dana R.'s `brand-agent-authoring` persona — the agent does **not** mint a second voice); human-*on*-the-loop review of the agent's bid/creative choices every Monday even though each action is within budget.

**Audit-log field spec (per agent action):** triggering instruction · interpreted action · before/after (or version-history link) · dollar impact · tier exercised · approver (if any) · timestamp · **cross-surface reconciliation status** (does this promo match what `agentic-commerce-readiness` exposes to outside shopping agents?). Target: Shopify activity log + BigQuery; retention 12 months; monthly review.

**Cross-surface reconciliation:** any discount the admin agent sets is checked against `pricing_guardrails` **and** against the syndicated catalog price outside shopping agents see — an internally-set promo and the externally-visible price never diverge. App-extension de-confliction: Klaviyo (email) and Sidekick can both touch a promo → **single lever owner = Merch Manager**, precedence = Sidekick drafts, Klaviyo sends; they do not both edit the promotion object.

**Rollback / kill switch:** draft-discard for Suggest tier; theme-version restore for theme edits; promo-deactivate + price rollback for pricing; transaction reversal for refunds. Auto-pause to Suggest-only on: error rate > 5%/day, ad-spend > 110% of the $3,500 envelope, a chargeback/complaint spike, or any brand-voice incident. Named kill switch: **Priya S.**, documented halt procedure.

**Staged rollout:** Phase 1 — Observe/Suggest on catalog cleanup only (retag drafts, dup-SKU merges) for a clean 2-week audit window. Phase 2 — promote dup-SKU merge to Autonomous-within-budget + enable Campaign Autopilot inside the $3,500 envelope. **No customer-visible money-moving task reaches an autonomous tier in Phase 1.** Promote metric: approval-acceptance ≥ 95%, error rate < 2%, $-variance < 5%; demote on any breach.

**Config-utilization checklist:** ✅ `voice_owner` (Dana R.) · ✅ `pricing_guardrails` (55% floor, 15% max autonomous depth) · ✅ `promotion_guardrails` (read from source, not redefined) · ✅ `approval_roles` (Merch Mgr / Web Lead / CX Sup) · ✅ `audit_log_target` (activity log + BigQuery) · ✅ `kill_switch_owner` (Priya S.) · ✅ `regulated_categories` (none in scope). Brand-voice and promotion rules cited as **read from** `brand-agent-authoring` / `promotion-campaign-builder`, not authored here.

## Notes

- The agent absorbs toil; it does not absorb accountability. Every customer-visible, money-moving, or irreversible action keeps a named human owner — the value is in moving the human from *doing* the task to *approving* or *spot-checking* it, not in removing the human from the loop.
- This is the merchant-side mirror of `agentic-commerce-readiness`. That skill makes the store legible to *outside* shopping agents; this skill governs the *inside* agent that runs the store. The two reconcile on price, promotion, and brand voice through the single `voice_owner` so the merchant never shows one face internally and another to a shopping agent.
- Brand voice has one source of truth. The admin agent reads the `brand-agent-authoring` persona; it does not author a second voice from its campaign or sales-assistant outputs. Any drift is a `voice_owner` escalation.
- Autonomous-within-budget is earned, not granted. A task reaches it only after a clean audit window at a lower tier, and it stays there only while its error rate, dollar variance, and brand-voice flags stay inside the ramp thresholds. The kill switch is a named human with a written procedure.
- App extensions acting through the admin agent are agents too. Where two agents can move the same lever, assign one owner and a precedence rule before turning either loose, or they will race on the same promotion.
