# Module: Kustomer

Type: helpdesk channel. UI: **App Store → Channels → Kustomer → <account>**.

## Workflows

One, shared by all helpdesks: **Add tag to ticket** (write). Needs the tags to add.

## Settings available in the UI

- **Active** — enable or disable the integration.
- **Ingest toggles**: email, SMS, chat, contact form, Instagram, Facebook, Twitter, WhatsApp, Facebook DM, Twitter DM, Instagram DM.
- **Excluded integrations** — full-list replace.
- **Skip mentions**.

Kustomer has no Agent Copilot widget. Where Gorgias creates one automatically, Kustomer requires a manual install from the settings screen.

## Conversation fields

Kustomer has a **Conversation Fields** section on the settings screen, listing the custom attributes synced from Kustomer. Editable per field: the **Active** toggle and the **Description** that tells Siena how to fill it.

Siena fills a field only when it is **active**. Unlike Gorgias, a required field that is inactive is skipped, not filled. Fields without a slug in Kustomer are skipped regardless.

Siena tags a ticket **`siena-close-failed`** when Kustomer refuses to close it — an empty required field is a common cause.

## Effects outside Siena

Turning the integration off disables Siena's triggers inside Kustomer.

## Not configurable in the UI

- Skip agent-assigned tickets — stored, no control.

## Direct link

`https://app.siena.cx/channels/kustomer/<uuid>`

Take `<uuid>` from `integrations[].uuid` on this channel's `list_modules` row — one entry per connected account, each with its own `name` and `active` flag. Name the account when a merchant has more than one. With no UUID to hand, link to the account list: `https://app.siena.cx/channels/kustomer`
