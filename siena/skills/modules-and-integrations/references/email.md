# Module: Email

Type: channel. UI: **App Store → Channels → Email → <account>**.

## Workflows

One, shared by all helpdesks: **Add tag to ticket** (write). Needs the tags to add.

## Settings available in the UI

- **Name** — connection display name.
- **Active** — enable or disable the integration.
- **Preferred email sender** — the address Siena sends from. It must be on the connection's own verified domain; an address on another domain is rejected. Can be cleared.
- **Excluded senders** — email addresses Siena ignores. Full-list replace; an empty list clears it.

Other tabs on the same screen: **DNS records** (the records to add for domain verification), **Suppressions** (addresses that bounced or unsubscribed), **Forwarding instructions**.

## Fixed after setup

The customer domain and the region are set when the connection is created and cannot be changed afterwards. Changing either means a new connection.

## Direct link

`https://app.siena.cx/channels/email/<uuid>`

Take `<uuid>` from `integrations[].uuid` on this channel's `list_modules` row — one entry per connected account, each with its own `name` and `active` flag. Name the account when a merchant has more than one. With no UUID to hand, link to the account list: `https://app.siena.cx/channels/email`
