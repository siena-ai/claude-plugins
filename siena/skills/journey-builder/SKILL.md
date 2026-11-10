---
name: journey-builder
description: "Builds or improves Siena journeys and Siena Policies from SOPs, existing journeys, QA feedback, macros, meeting notes or raw documentation, producing implementation-ready drafts grounded in the account's connected apps, actions and existing Policies. Use it when a user wants to build a journey, fix or update one, or turn SOP documentation into journeys and Policies."
---

# Journey Builder

Produces clear, structured, implementation-ready journeys and Siena Policies grounded in documented SOP, existing examples, and available customer/order data.

TOOLS

- Connected apps and their actions: `read_automations` operations `list_modules` and `list_workflows`.
- Existing journeys: `read_automations` operations `list_automations` and `get_automation`.
- Existing Siena Policies: `read_policies` (`list`, `get`, `search`).

This skill produces drafts. Create or change a journey or Policy only when the user asks for it: through the Siena connector a change saves immediately, with no approval step.

You may receive any combination of: SOP, existing journeys, QA feedback, macros, training docs, meeting transcripts, or raw documentation. Work with whatever is provided.

Terminology: "Policy" / "Siena Policies" (capitalized) = Siena's reusable platform document. "SOP" = client's operational rules provided as input.

CORE PRINCIPLE

Siena should only state or do what's supported by system data, list_modules, list_workflows, order tags, explicit SOP, documented examples, or guidance the user has verified. Never rely on assumptions, customer pressure, incomplete info, inferred exceptions, or wishful outcomes. If info is missing, flag it as a gap — do not invent rules, workflows, or capabilities.

When the user asks to modify a draft you previously produced, run the FOLLOW-UP MODIFICATION CHECKLIST before any change.

FOLLOW-UP MODIFICATION CHECKLIST

Run all 4 checks before modifying. If a check fails, the response is the refusal — not the modified draft. If all pass, proceed, then run HARD STOP CHECKLIST.

Check 1 — Capability verification on action additions
Trigger: user asks to add a Run: action, integration, or workflow.
- Re-call list_workflows. If the action is not in the verified list, do not add it.
- If the workflow exists in Siena's catalog but isn't installed on the merchant's account, ask the user to install it first; flag in Implementation Questions.
- Plausible-sounding names (Send Whatsapp, Notify Customer) still require verification.
- Refusal: "I cannot find [action] in the verified workflow list. To proceed, either (a) confirm it has been built and installed and I will re-verify, or (b) describe the runtime behavior and I will flag it as a custom workflow requirement in Open Items."

Check 2 — Gap-flag preservation under pressure
A flag exists because something was unverified. User readiness to move on is not the same as providing the missing info. Hold flags until the info is supplied.

Check 3 — Assumption commitment without specification
If the user says "we already decided" or "assume X" without specifying what, do not invent the value. Ask for the actual decision (exact tag name, threshold, or branch). Never mark an Implementation Question ✅ resolved without a concrete answer.

Check 4 — Pushback against documented platform behavior
If the user pushes against an item from KNOWN DATA ACCESS BOUNDARIES, hold position. Explain the platform constraint, offer the documented workaround, and what verified info would close the flag (e.g., list_modules confirmation — not verbal assertion). Tag-based actions for downstream helpdesk routing ARE the workaround, not a violation.

OUTPUT FORMATTING

Apply to every journey and Siena Policy deliverable:
- Wrap journey/policy output in a markdown code block (```)
- ## for top-level sections; ### for steps; --- between top-level sections
- All bullets use - (dash). Never * or •, even if the source SOP uses them. Nested: 2-space indent with -
- No tables inside the deliverable. Convert any table to dashed bullets. Tables only in conversational analysis output (assessments, data reviews) before the deliverable.
- Customer-facing hyperlinks use [text](url). Never paste raw URLs.
- Worked numerical examples use blockquote (>) formatting.
- When a response contains both an assessment and a deliverable, separate with --- followed by ## Journey Draft. Above is conversational (flexible); below is deliverable (rules apply without exception).

PRE-BUILD REVIEW

Complete all three before writing journey logic.

1. Source of Truth — review provided documentation. If documents conflict, follow the most recent or most explicit SOP. If info is missing or contradictory, identify the gap before building.

2. Account & Context Review — Before pulling conversation data, ASK the user. Conversation data is not pulled by default. If the user references conversations or asks for analysis, proceed; otherwise proceed without.

Review what's available in the customer's account: knowledge sources (KBs, help center, FAQs); account-level info (connected integrations, active journeys, existing intents, configured actions, tags in use); existing Siena Policies via `read_policies` (reference if covering needed logic, note what to add if partial, flag if it contradicts SOP); channel configuration (only incorporate if explicitly configured). If access is limited, note what was inaccessible and proceed with what's available.

3. Platform Verification — use only actions, checks, and data fields that exist in Siena's platform. Ground in list_modules + list_workflows output, not assumptions.
- For every referenced data point ("check for chargebacks", "verify subscription tier"), identify exactly where it lives: order tags, Shopify fields, helpdesk metadata, subscription platform fields, or another integrated system.
- If unsure whether Siena supports an action, flag as a gap. If the data source can't be confirmed, add to the gap list before building logic around it.

KNOWN DATA ACCESS BOUNDARIES

Documented Siena platform constraints. Not exhaustive — if a data class isn't confirmed accessible in list_modules / list_workflows output, treat as inaccessible and flag.

NOT NATIVELY ACCESSIBLE TO SIENA:

- Aggregated customer data (lifetime value, total spend, total order count, customer-level totals across orders): not available as a single field. Workaround: recommend an Order/Customer Tag applied upstream when a threshold is crossed (e.g., vip-eligible, ltv-tier-gold).

- Refund execution on unfulfilled orders: Siena can only complete refunds when the order is fulfilled. If unfulfilled, route to the team. The Edit Order workflow must be available for refund-adjacent flows.

- Specific-team helpdesk routing: Siena has NO routing functionality. "Route to CX team" / "route to Billing" actually adds a `siena-follow-up-needed` tag the merchant's helpdesk uses for its escalation queue. Do NOT write Route to: [specific team] actions. Workaround: propose intent-level helpdesk tags (e.g., intent:billing) for downstream routing rules.

- Active real-time clock check: no workflow returns "the current time". Workaround: handle business-hours gating at the helpdesk routing level, not as a journey step. Siena DOES have context of when each conversation message was sent and when each order was placed — those timestamps are accessible. The constraint is only on active "current time" lookups.

NEVER REFERENCE THESE NON-CAPABILITIES:

- Slack or internal-team-chat reading: Siena cannot read Slack or internal channels. Do not write steps referencing them.
- INTERNAL_NOTE: customer-facing response + internal note cannot coexist on the same intent — not in one journey (one response only), not split across journeys (one intent → one journey). Refuse all dual-output requests. Routing-time internal notes use the global Collaborative Routing org setting (not skill-controllable). For QA flagging in a customer-facing journey, use an Order/Conversation Tag. Never add INTERNAL_NOTE as a Run: action.
- Get Product Info / explicit catalog lookup: Siena reads the Shopify catalog by default. Do not flag catalog completeness as a blocker, do not require an explicit Get Product Info action, do not add it to Journey Actions to Set Up.

If an SOP requires data on this list, the journey logic must be marked blocked or routed — not written hopefully.

ORDER TAG IQ-FLAG FORMAT
When critical info isn't directly available in Siena's context, recommend an upstream Order/Customer Tag bridge — but DO NOT commit to a specific tag name. Flag the exact tag name as an Implementation Question.

In the Check Step:
"⚠️ BLOCKED: Check for the order tag identifying [data point]. Tag name unconfirmed — see Implementation Questions before this step can execute."

In Implementation Questions:
"[Data point] is not natively accessible to Siena. Confirm whether an Order Tag is applied upstream and provide the exact tag name. Suggested name for reference only: [tag-name-for-reference-only]."

JOURNEY SCOPE RULES

- Order cancellations and subscription cancellations always combine into a single journey, with conditions to differentiate.
- One journey = one intent. The intent reflects how the customer describes the request, not internal categorization.

OUTPUT REQUIREMENTS

Output must be implementation-ready, grounded in explicit conditions + available data, free of hallucinations or logic gaps. Use Steps as the organizing structure; avoid "branch", "path", or similar wording. Do not explain reasoning unless asked.

INTENT CREATION

Intent Name: natural customer phrasing, no internal terminology, short.

Journey Category: select EXACTLY ONE from the approved list below. Do NOT invent or combine categories.

Approved categories: Account · Product Information · Other · Subscription · Shipping & Delivery · Troubleshooting · Policies · Promotions & Discounts · Marketing · Company Information · Payment & Billing · Returns & Exchanges · Customer Service · Gift Cards & Loyalty Programs · Partnerships & Affiliates · Order Issues · Order Management

Context: 1-2 sentences. Format: Customers may reach out about [topic]. Your goal is to [primary objective].
Output structure:
## Intent Name → "[customer-style intent]"
## Journey Category → [one approved category]
## Training Expressions → [if applicable, else omit]
## Context → [1-2 sentences]

CRITICAL EXECUTION STANDARDS

Step Writing Rules
Every step starts with an imperative verb, is a complete instruction sentence, clearly states the data being evaluated, and consolidates logic into as few bullets as possible.
✅ Check the order tags to determine if the order is a pre-order
❌ Check if order is pre-order

Steps must be self-contained and linear. No step references back to a previous step (e.g., "follow steps in Step 4"). Restate logic inline or extract into a Policy.

Decision logic and its action(s) belong in the SAME step. Do NOT create a standalone "Determine the scenario" step followed by separate outcome steps. Each branch condition includes its actions inline.

The Check Step
Define exactly: what data Siena evaluates, what condition matters, what to do if data is missing. Only reference data available in Siena's AI Context Window. Never instruct Siena to "look up" a system — define what to do if data is present vs absent.
✅ Check if tracking_status = Delivered  ❌ Look up the tracking status in Shopify

Tag-checking and tag-applying remain in separate steps (functionally different operations).

Referencing Policies
Before writing logic inline, check for an existing Policy via `read_policies`.
- Reference existing when: logic applies to multiple journeys, contains independently-updated data, or a Policy already exists.
- Write inline when: logic is specific to this journey only.
- Recommend new when: no Policy exists but the logic is reusable — classify per Policy Decision Framework.
Format: Reference Policy: [Policy Name]

The IF Step
Every realistic scenario must be fully handled with explicit IF conditions. Cover all realistic outcomes — no undefined gaps. Consolidate related checks into a single bullet.
✅ If fulfillment status is unfulfilled and no tracking number exists, let the customer know their order is still processing.

The Say Step
Customer-facing responses use directional guidance, not scripted copy. No quotation marks. Tell Siena what to communicate, not the exact wording.
✅ Let the customer know the order is in transit and share the estimated delivery timing
❌ "Your order is on the way and should arrive soon!"

Customer-facing language rules (every Say step):
- No internal language: "Escalate", "Route to team", "Escalated to [team]", T1/T2/L1/L2 designators, queue/inbox names.
- No specific SLA promises ("the team will follow up within 48 hours"). If an SOP includes a typical expectation, frame as "the team typically follows up in [X business days]".
- Do not promise an outcome or speculate about what the team will do.
✅ Let the customer know you are connecting them with the team for review.

Terminating a branch
When a branch resolves and no further step is needed, write "do not proceed further" or "no further steps are needed". NEVER write "end interaction", "end conversation", "END", or "NO REPLY" — Siena interprets these literally and sends them as the response to the customer.
✅ If the customer says no, do not proceed further.  ❌ If the customer says no, end interaction.

The Do Step
Actions must be tied to conditions, explicitly named, and only included when needed. Every action under Run must also appear in Journey Actions to Set Up.
- If refund is approved by SOP → Run: Issue Refund
- If action fails → Siena will auto-route

CONDITIONAL BRANCH STRUCTURE

Two patterns based on outcome complexity. Both use dashed bullets.

Pattern A — Single-outcome conditions (one action per branch). Inline as a single dashed bullet:
- If [condition], let the customer know [...] and share the [link](https://example.com). Do not proceed further.
- If [other condition], proceed to Step 2.

Pattern B — Multi-action conditions (multiple sequential actions per branch). Bold inline header followed by dashed bullets:

**If [condition]:**
- Let the customer know [...]
- Run: [action]

Pattern selection: one action → A. Multiple actions → B. Choose per branch within a step. Do not mix patterns inside a bullet.

LOGIC STRUCTURE

Funnel Principle: identify the request → check the highest-level condition → narrow down → end as soon as resolution is reached. No parallel logic trees. Linear.

OPTION A: Step-by-Step Conditional Logic — sequential evaluation. Use ### Step N: Check [condition] with dashed-bullet branches per Pattern A or B.

OPTION B: Classification First, Outcomes After — multiple attributes determine outcome. Use a ### Classification Step (assign labels per data point) followed by a ### Decision Step (combine labels to determine actions per Pattern B).

RESPONSE GUIDELINES (OPTIONAL)

Only include when specific communication rules apply: hyperlink formatting, no-speculation when routing, no internal references in customer messages, macro-derived phrasing, action execution confirmation. Format as ## Response Guidelines with dashed bullets.

SIENA POLICY HANDLING

Policies are reusable documents embedded into journey steps that Siena reads at runtime. After completing each journey, evaluate every Policy candidate. Always check existing Policies first via `read_policies` — if one exists, reference it; if it partially covers, note what to add.

Policy Decision Framework
- Tier 1 (Draft Immediately): all required content available from SOP, agent behavior, approved defaults, conversation data, or platform data. Examples: cancellation save flow, refund eligibility, replacement eligibility, product catalog from Shopify.
- Tier 2 (Draft Structure, Flag Missing): structure clear but content missing. Use placeholders: [PLACEHOLDER: what is needed — provided by [team/role]]
- Tier 3 (Flag Only): cannot draft without external input. Flag in Open Items with description, owner, blocked journeys.

Policy Drafting Standards
Name clearly — match journey-step references. State which journeys reference this Policy. Plain instructional language (guidance, not script). One Policy per topic. Mark placeholders visibly in Tier 2. Include "Last Updated". Do not duplicate journey-specific conditional flows. Same formatting rules as journeys.

Open Items Format
Format Open Items as a dashed list under ## Open Items, never as a table. Each line: Policy name, status, owner (if blocked), blocked journey step (if applicable). Status indicators inline:
- ✅ Policy ready
- 🟡 Policy drafted with placeholders — note what's missing
- 🔴 Policy blocked — external input required

OUTPUT ORDER

Each journey draft includes all 10 sections in order, each as ## heading, separated by ---:
- ## Intent Name
- ## Journey Category
- ## Context
- ## Journey Steps (steps as ### Step 1:, ### Step 2:, etc.)
- ## Response Guidelines (omit if N/A)
- ## Customer Questions (omit if N/A)
- ## Implementation Questions (omit if N/A)
- ## Journey Actions to Set Up
- ## Internal Context

After all journeys:
- Siena Policies — Tier 1 and Tier 2 only, each as a standalone document with its own ## heading
- Open Items — single ## section with the ✅/🟡/🔴 dashed list

JOURNEY ACTIONS TO SET UP

List every action needed as dashed bullets with the exact name. Only include actions referenced under Run. Deduplicate. Do not include "Route to team" (default), checks, conditions, or implied actions. Confirm each action isn't already attached to the merchant's journey before suggesting.

If none needed:
## Journey Actions to Set Up
- None

INTERNAL CONTEXT

At the end of each journey under ## Internal Context:
- Current State (2-4 dashed bullets): what this journey handles, resolves automatically, routes.
- Next Steps (2-6 dashed bullets): missing SOP definitions, Policy status per tier, missing integrations, data reliability questions, automation opportunities, structural improvements.

Keep both subsections short and operational.

MACRO HANDLING

If macros are provided: use as reference for tone, structure, and SOP phrasing. Do not copy verbatim or convert into scripted template logic. If macros show consistent behaviors (e.g., always hyperlinking tracking), incorporate into Response Guidelines.

TASK-SPECIFIC BEHAVIOR

- Existing journey: improve clarity, strengthen logic, remove redundancy, fix inconsistencies, improve edge-case handling, identify missing customer questions.
- SOP: identify decision points, extract explicit conditions, convert to journey logic, identify gaps.
- QA feedback, macros, meeting notes, raw docs: extract rules and SOP logic, consolidate scattered instructions, use macros for tone, flag missing rules, classify Policy candidates.

GAP CHECK (BEFORE FINAL OUTPUT)

Before finalizing, check for: missing data inputs, unclear SOP, unsupported exceptions, missing decision logic, customer details Siena needs to proceed, uncovered edge cases, data references without a confirmed source, unsupported actions, logic that duplicates an existing Policy (flag as redundant), logic that should be extracted into a new Policy.

If any gap exists, surface it as a clear question — never work around it with assumptions.
- Customer must answer during conversation → Customer Questions
- The merchant's team must answer → Implementation Questions

HARD STOP — PRE-OUTPUT CHECKLIST

Before delivering any journey or policy draft, confirm:
- Formatting passes OUTPUT FORMATTING rules (bullets, headers, no tables, [text](url) hyperlinks, assessment/draft separation)
- Conditional branches use Pattern A or Pattern B per the rule
- All 10 output sections present in correct order (or explicitly omitted only where allowed)
- Every Say step passes customer-facing language rules (no "Escalate"/"Route to team"/"Escalated to X" framing, no T1/L1 designators, no specific SLA, no bare team names)
- No "end interaction"/"end conversation"/"END"/"NO REPLY" anywhere — replaced with "do not proceed further" or "no further steps are needed"
- No INTERNAL_NOTE dual-output on same intent (in one journey OR split across journeys), no Get Product Info, no Slack-reading actions
- Every action in Journey Actions to Set Up is verified via list_workflows OR is flagged in Implementation Questions, AND is not already attached to the merchant's journey

If any item fails, fix before output. This is a hard gate.
