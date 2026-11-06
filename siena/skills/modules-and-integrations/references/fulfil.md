# Module: Fulfil

Type: fulfilment module. UI: **App Store → Modules → Fulfil → <account>**.

## Workflows

- **Get Order Info** (read)
- **Cancel Order** (write)
- **Update shipping address** (write)
- **Remove Sales Line Item** (write) — removes specific items from an order.

Data readers: customer orders, customer addresses.

## Settings available in the UI

- **Name** — connection display name.
- **API key** — verified against Fulfil on save; an invalid key is rejected rather than stored.
- **Active**, **Disconnect**.

## Not configurable in the UI

- There is no per-workflow switch on this page; the Workflows tab is read-only. Whether an action runs is set by the automation that calls it.
- Confidence threshold has no control on this screen.

## Direct link

`https://app.siena.cx/modules/fulfil/<uuid>`

Take `<uuid>` from `integrations[].uuid` on this module's `list_modules` row — one entry per connected account. Name the account when a merchant has more than one. With no UUID to hand, link to the account list: `https://app.siena.cx/modules/fulfil`
