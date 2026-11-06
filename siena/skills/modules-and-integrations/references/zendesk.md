# Module: Zendesk

Type: helpdesk channel. UI: **App Store → Channels → Zendesk → <account>**.

## Workflows

One, shared by all helpdesks: **Add tag to ticket** (write). Needs the tags to add.

## Settings available in the UI

- **Active** — enable or disable the integration.
- **Ingest toggles**: email, SMS, chat, contact form, API, Instagram, Facebook, Twitter, Facebook DM.
- **Reply only to new tickets** — Siena replies only to newly created tickets.
- **Excluded integrations** — Zendesk integration IDs to ignore. Full-list replace.
- **Skip mentions**.

## Social DMs are deliberately not supported

WhatsApp, Twitter DM and Instagram DM are **not** available on Zendesk. Zendesk's social DM bridge does not deliver Siena's replies, so those toggles are intentionally absent rather than missing by oversight. Facebook DM is supported and verified.

If a merchant wants Siena on Instagram or WhatsApp DMs, the route is the Meta channel, not Zendesk.

## Conversation fields

Zendesk has a **Conversation Fields** section on the settings screen, listing the ticket fields synced from Zendesk. Editable per field: the **Active** toggle and the **Description** that tells Siena how to fill it. Field name and required flag come from Zendesk.

Zendesk's field filling is **form-aware**, unlike the other helpdesks: Siena works from the active ticket forms, and the form-aware path is skipped when the account has one active form or none. An inactive field is skipped even if Zendesk marks it required.

Siena tags a ticket **`siena-close-failed`** when Zendesk refuses to close it — an empty required field is a common cause. If a merchant reports tickets not closing, check the field's Active toggle first.

## Effects outside Siena

Turning the integration off disables Siena's triggers inside Zendesk. For a Zendesk Chat connection it deregisters the websocket instead.

## Not configurable in the UI

- Help-centre article sourcing fields — stored, no control.
- Skip agent-assigned tickets — stored, no control.

## Direct link

`https://app.siena.cx/channels/zendesk/<uuid>`

Take `<uuid>` from `integrations[].uuid` on this channel's `list_modules` row — one entry per connected account, each with its own `name` and `active` flag. Name the account when a merchant has more than one. With no UUID to hand, link to the account list: `https://app.siena.cx/channels/zendesk`
