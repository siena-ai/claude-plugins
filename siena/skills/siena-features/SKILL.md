---
name: siena-features
description: "Siena's features and how they fit together: the core Support Agent (how a customer message becomes a reply, Journeys, Policies, Personas, Business Context, Knowledge Sources, Docs, actions and handoff), the Shopping Agent, Social Agent, Siena Live Chat and Ask Siena, plus which features an organization may or may not have, the roles that gate each screen, and the words the dashboard uses. Load this before describing what Siena can do, before saying a feature does or does not exist, and before answering whether Siena can do something."
---

# Siena Features

What Siena is, what a merchant actually has, and the words they see. Load before
describing Siena's capabilities, before saying a feature does or does not exist,
and before answering "can Siena do X".

Detail for each agent is in this skill's `references/` folder. Read the
state, then read the reference.

## The core: the Support Agent

Siena is a customer support agent. Everything else is a channel it works in, an
agent specialised for one surface, or tooling around it.

A customer message goes through: **arriving** on a connected channel →
**Journey matching** (each Journey's learning phrases are the examples Siena
matches against; a match is a yes or no per Journey, not a score anyone can
tune) → **knowledge retrieval** from Knowledge Sources and Docs → **deciding
what to do** from the Journey's instructions, its Policies and the customer's
context → **responding** in the channel's Persona voice → an automatic
**quality check** that can downgrade a reply to an internal note or force a
handoff.

That check runs **before sending on every channel except Siena Live Chat**,
where it runs after the reply has gone out. So on Live Chat a poor reply can
reach the customer and be corrected afterwards.

**Siena can also answer with no matching Journey.** Conversational, intentless
and generative-resolution answering are enabled per channel group — which is why
a good answer sometimes has no Journey behind it, and why turning those off
makes Siena go quiet on anything unmapped.

Two things about actions, which are what make Siena more than a chatbot:

- Actions come from two places. **Journey steps** are built into every
  Journey and need no App (below). **App actions** come from connected
  **Apps** — cancel an order, refund, change an address, skip a shipment,
  adjust loyalty points. What App actions Siena *can* run depends entirely on
  what is connected, so use the `modules-and-integrations` skill before any
  integration-dependent claim.
- An org can add its own as **Custom Apps**, so the action list is extensible
  per customer and never fully enumerable from memory.

## Journey steps

Every Journey can include these, on every organization, with no App
connected. They are added from the toolbar under a Journey's response.

| Step | What it does |
|---|---|
| **Send Document** | Attaches a file the merchant uploads to Siena's reply. Siena refers to it in the reply text. Where a channel cannot carry attachments (SMS, X/Twitter), the file goes out as a link at the end of the reply |
| **Send Image** | The same, for an image |
| **Tag** | Tags the conversation when the Journey runs |
| **Status** / **Snooze** | Opens, closes or snoozes the ticket |
| **Internal Note** | Leaves a note for the team instead of a customer reply |
| **Notification** | Notifies a Slack channel when the Journey runs |
| **Skip** | Siena does not reply; tags and status changes still apply |

**Handoff is a first-class outcome, not a failure.** Siena routes to a human,
tags the conversation, and can hold a reply as an internal note instead of
sending. **Automatic drafting is Meta-only** — on a helpdesk the equivalent
hedge is an internal note, so never offer "draft mode" on email or chat.

## The words to use

Use the merchant's words. The third column is what never to say back to them.

| Term | What it is | Never call it |
|---|---|---|
| **Journeys** | The request types Siena recognises and answers | "automations" or "intents" |
| **Policies** | Reusable rules referenced from Journeys | |
| **Persona Studio** | Voice and tone, per channel | |
| **Business Context** | Company-wide facts, shared across channels | |
| **Knowledge Sources** | Help centres, Shopify pages, past conversations, websites, PDFs | |
| **Docs** | The merchant's own documentation, at `/knowledge`. Also where Skills live | |
| **Memory** | What Siena remembers about their *end customers* | |
| **Generative Resolutions** | Answering from knowledge with no Journey | |
| **Thinking time** | The wait before replying | |
| **Apps** / **Custom Apps** | Connected systems. An App's capabilities are its **Actions** | "modules" |
| **Shopping Agent** | The selling agent | "Sales Agent" — though one older screen still says "Sales Agent Guidance" |
| **Social Agent** | Facebook and Instagram comments and DMs | |
| **Siena Chat** / **Live Chat** | Siena's own widget | |
| **Ask Siena** | Siena's assistant inside the dashboard | any other name for it that shows up in a URL or a tool result |
| **Text** | The SMS and WhatsApp channel group | "SMS" — a few older screens still do |

Channel groups as the merchant sees them: Email, Live Chat, Text, Comments, Meta
Comments, Meta DMs, DMs, Slack, Reviews, Voice, Other Channels.

## The agents

Four genuinely different things. Do not describe one as a mode of another.

| Agent | What it does | Detail |
|---|---|---|
| **Support Agent** | The core: answers customers, runs actions, hands off | this skill |
| **Shopping Agent** | Recommends products; replaces knowledge retrieval on a shopping message | `references/shopping-agent.md` |
| **Social Agent** | Reads and moderates Facebook and Instagram comments and DMs | `references/social-agent.md` |
| **Ask Siena** | Works **on the account** for the operator, not on customer conversations | — |

**Siena Live Chat** is a channel, not an agent — but it is the only one Siena
owns end to end, and where the Shopping Agent's buy button and starters render:
`references/siena-live-chat.md`.

## Availability: what is on, and who turns it on

Not every organization has every feature. **A feature can be on for one
organization and off for another, and switching one on is something Siena
does** — so when a feature is unavailable the answer is to talk to Siena, never
"it doesn't exist" and never "that needs a bigger plan". **Nothing is gated by
subscription level.**

**A visible screen does not mean the feature is running.** Navigation and
capability are separate: the Shopping Agent page is reachable by everyone while
the agent behind it may be off, and the Social Agent page and its comment
automation are switched on independently. So a reachable screen is not evidence
a feature is on, and an empty one is not evidence it is broken — check the
feature's own state, as the references describe.

A feature part-way through release stays consistent within one conversation and
can differ in the next, so a merchant reporting inconsistency between
conversations is not imagining it.

## Roles

Four, and each one adds to the last.

| Role | Adds |
|---|---|
| **Agent** | Views conversations and Siena's reasoning, operates tickets, full authoring on Journeys, Policies, Personas, Knowledge Sources, Docs |
| **Manager** | User management, API keys, full control of Evaluations |
| **Administrator** | Channel and Module settings, Stores, the billing screens |
| **Owner** | Creating and deleting the organization |

In practice: **Channel and Module settings need Administrator or Owner**;
Analytics needs a reporting permission an Agent lacks; most AI and configuration
settings tabs need an inbox-configuration permission; an Owner cannot be
removed; and Manager and Agent cannot change roles.

## Rules

1. **Never answer "Siena can't do that"** for anything depending on configuration, a connected app, a Journey step, or whether the feature is switched on, without checking first.
2. **The Siena tools cannot change which features are on, a connected integration's
   or channel's configuration, Social Agent settings, or Live Chat settings.**
   Name the setting, give the path, say what it
   makes possible — never imply a change was made.
3. **Do not name a setting that is not in one of these skills.** Several exist
   with no control on any screen; sending a merchant looking for one that is not
   there is worse than saying you do not know.
4. **Distinguish "off" from "absent".** Off can be switched on. Absent cannot —
   some channels have no setting at all. When a merchant describes a change they
   already made, confirm it in the stored settings before relying on it.

## References

- `references/shopping-agent.md` — the Shopping Agent
- `references/social-agent.md` — the Social Agent
- `references/siena-live-chat.md` — Siena Live Chat

Related skills in this plugin: `modules-and-integrations` (every app — what is
connected, what actions it runs, what is configurable and where; use it before
any integration-dependent claim), `data-analytics` (use it before any data,
voice-of-customer, topics or evaluations question), and
`configuring-the-shopping-agent` (making Shopping Agent changes; it needs the
`read_shopping_agent` and `manage_shopping_agent` tools, and if
`read_shopping_agent` is not available this organization does not have it).

## Links

At `https://app.siena.cx`: Journeys `/automation/knowledgebase`, Policies
`/policies`, Persona Studio `/persona-studio`, Business Context
`/persona/business-context`, Knowledge Sources `/data-sources`, Docs
`/knowledge`, Inbox `/inbox`, Analytics `/insights`, Shopping Agent
`/shopping-agent`, Social Agent `/social`, Ask Siena `/insights-chat`, Campaigns
`/campaigns`, Planner `/platform/planner`, App Store `/app-store`, Settings
`/settings`.
