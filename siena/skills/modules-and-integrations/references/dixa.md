# Module: Dixa

Type: helpdesk channel. UI: **App Store → Channels → Dixa → <account>**.

## Workflows

One, shared by all helpdesks: **Add tag to ticket** (write). Needs the tags to add.

## Settings available in the UI

- **Name** — connection display name.
- **Active** — enable or disable the integration.
- **Ingest toggles**: email, SMS, contact form, API, widget chat, messenger, Facebook Messenger, WhatsApp, Twitter.
- **Handoff queue** — the Dixa queue ID Siena hands conversations to. Can be cleared.
- **Included messenger domains** — restricts messenger conversations to specific domains.
- **Excluded integrations** — full-list replace.
- **Skip agent-assigned tickets**, **Skip mentions**.
- **Refresh custom fields** — re-pulls Dixa's custom field definitions.

Dixa is the only helpdesk where the skip and handoff-queue settings are fully specified server-side; the equivalents elsewhere are looser.

## Conversation fields

Dixa has a **Conversation Fields** section on the settings screen, listing the custom attributes synced from Dixa. Editable per field: the **Active** toggle and the **Description** that tells Siena how to fill it. Use **Refresh custom fields** to re-pull the definitions after changing them in Dixa.

Siena fills a field only when it is **active** and not already filled on the conversation. An inactive field is skipped even when required.

Siena tags a conversation **`siena-close-failed`** when Dixa refuses to close it — an empty required field is a common cause.

## Effects outside Siena

None on update. Dixa is configured entirely on Siena's side.

## Direct link

`https://app.siena.cx/channels/dixa/<uuid>`

Take `<uuid>` from `integrations[].uuid` on this channel's `list_modules` row — one entry per connected account, each with its own `name` and `active` flag. Name the account when a merchant has more than one. With no UUID to hand, link to the account list: `https://app.siena.cx/channels/dixa`
