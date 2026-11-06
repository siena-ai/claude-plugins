# Module: Gladly

Type: helpdesk channel. UI: **App Store → Channels → Gladly → <account>**.

## Workflows

One, shared by all helpdesks: **Add tag to ticket** (write). Needs the tags to add.

## Settings available in the UI

- **Active** — enable or disable the integration.
- **Ingest toggles**: email, SMS, chat, WhatsApp, Facebook DM, Twitter DM, Instagram DM.
- **Handoff inbox** — the Gladly inbox ID Siena hands conversations to. Handoff does not work without it.
- **Skip agent-assigned tickets**.

## Effects outside Siena

Webhooks are created when the integration is connected and removed when it is disconnected. Toggling Active does not touch them.

## Direct link

`https://app.siena.cx/channels/gladly/<uuid>`

Take `<uuid>` from `integrations[].uuid` on this channel's `list_modules` row — one entry per connected account, each with its own `name` and `active` flag. Name the account when a merchant has more than one. With no UUID to hand, link to the account list: `https://app.siena.cx/channels/gladly`
