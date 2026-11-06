# Module: Loop Returns

Type: returns module. UI: **App Store → Modules → Loop Returns → <account>**.

## Workflows

- **Generate Return Link** (write) — a return link for an order.
- **Get Return Status** (read)
- **Send Shipping Label** (read)
- **Cancel Return** (write) — needs the return ID.

All four read order context from **Shopify**, not from Loop. Loop Returns therefore needs a linked Shopify store to work.

## Settings available in the UI

- **Name** — connection display name.
- **Linked Shopify store** — which Shopify connection this account uses. Can be set to none, which breaks the actions above.
- **API key** — rotate the credential.
- **Active**, **Disconnect**.

## Not configurable in the UI

- There is no per-workflow switch on this page; the Workflows tab is read-only. Whether an action runs is set by the automation that calls it.
- Confidence threshold, retry count and confirmation prompts have no control on this screen.

## Direct link

`https://app.siena.cx/modules/loop-returns/<uuid>`

Take `<uuid>` from `integrations[].uuid` on this module's `list_modules` row — one entry per connected account. Name the account when a merchant has more than one. With no UUID to hand, link to the account list: `https://app.siena.cx/modules/loop-returns`
