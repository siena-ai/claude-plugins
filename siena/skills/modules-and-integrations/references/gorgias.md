# Module: Gorgias

Type: helpdesk channel. UI: **App Store → Channels → Gorgias → <account>**.

## Workflows

One, shared by all helpdesks: **Add tag to ticket** (write). Needs the tags to add.

Gorgias has no other first-party workflows. Actions on orders, subscriptions or returns come from the commerce module connected alongside it.

## Settings available in the UI

**Settings tab**

- **Active** — enable or disable the integration.
- **Ingest toggles**, one per Gorgias source: email, SMS, chat, contact form, API, Instagram, Facebook, Twitter, WhatsApp, Facebook DM, Twitter DM, Instagram DM. Each controls whether conversations from that source reach Siena.
- **Excluded integrations** — Gorgias integration IDs to ignore. Replaces the whole list on save.
- **Agent Copilot** — creates a Siena widget inside Gorgias for agents to use.
- **Skip mentions** — ignore tickets where an agent was mentioned.
- **Preferred email sender** — the address Siena replies from.

**Conversation Fields** — a collapsible section on the same settings screen, not a separate tab. One row per Gorgias custom ticket field, synced from Gorgias.

- Editable: **Active** toggle, and **Description** (tells Siena how to fill the field).
- Read-only: field name, required flag, type and choices. These come from Gorgias and change only in Gorgias.

## Conversation fields determine whether Siena fills them

Siena fills a conversation field only when that field is **active**, or is marked **required** in Gorgias. The required-overrides-inactive part is specific to Gorgias — on Zendesk, Kustomer and Dixa an inactive field is skipped even when required. If no field on the integration is active, Siena fills none of them — the field-filling step does not run at all.

Consequence: Gorgias refuses to close a ticket whose required field is empty. When that happens Siena tags the ticket **`siena-close-failed`** and the ticket stays open. If a merchant reports tickets not closing, or reports that Siena is not tagging or categorising, check whether the relevant field is active in the Conversation Fields section.

A field being present in the list is not the same as being active. Field names are the merchant's own and differ per account — never assume a field exists or is on.

## Effects outside Siena

- Turning the integration off disables **all of Siena's triggers inside Gorgias**.
- Turning Agent Copilot on creates a widget in the merchant's Gorgias account; turning it off deletes it.

## Not configurable in the UI

- Custom API base URL.
- Skip agent-assigned tickets — stored, no control in the UI.
- Field name and required flag — set in Gorgias.

## Direct link

`https://app.siena.cx/channels/gorgias/<uuid>`

Take `<uuid>` from `integrations[].uuid` on this channel's `list_modules` row — one entry per connected account, each with its own `name` and `active` flag. Name the account when a merchant has more than one. With no UUID to hand, link to the account list: `https://app.siena.cx/channels/gorgias`
