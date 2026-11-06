# Module: ShipMonk

Type: fulfilment module. UI: **App Store → Modules → ShipMonk → <account>**. Tabs: Settings, Workflows.

## Workflows

- **Get Order Info** (read) — by ShipMonk order ID or Shopify order number.
- **Cancel Order** (write)

Data reader: orders.

## Settings available in the UI

- **Store ID** — must be a positive whole number; anything else is rejected.
- **Rotate webhook credentials** — regenerates the webhook secret. The webhook URL and username are shown read-only for copying into ShipMonk.
- **Active**, **Disconnect**.

There is no name field. The API key is set at connection time and cannot be changed afterwards — rotating it means reconnecting.

## Not configurable in the UI

- There is no per-workflow switch on this page; the Workflows tab is read-only. Whether an action runs is set by the automation that calls it.

## Direct link

`https://app.siena.cx/modules/shipmonk/<uuid>`

Take `<uuid>` from `integrations[].uuid` on this module's `list_modules` row — one entry per connected account. Name the account when a merchant has more than one. With no UUID to hand, link to the account list: `https://app.siena.cx/modules/shipmonk`
