# Module: Intercom

Type: helpdesk channel. UI: **App Store → Channels → Intercom → <account>**.

## Workflows

One, shared by all helpdesks: **Add tag to ticket** (write). Needs the tags to add.

## Settings available in the UI

- **Active** — enable or disable the integration.
- **Excluded integrations** — full-list replace.
- **Sending admin** — which Intercom admin Siena posts as. Replies appear under that admin's name.

Intercom has the smallest settings surface of the helpdesks. There are no per-source ingest toggles: Intercom conversations arrive as one stream.

## Direct link

`https://app.siena.cx/channels/intercom/<uuid>`

Take `<uuid>` from `integrations[].uuid` on this channel's `list_modules` row — one entry per connected account, each with its own `name` and `active` flag. Name the account when a merchant has more than one. With no UUID to hand, link to the account list: `https://app.siena.cx/channels/intercom`
