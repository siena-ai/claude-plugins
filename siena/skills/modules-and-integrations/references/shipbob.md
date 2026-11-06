# Module: ShipBob

Type: fulfilment module. UI: **App Store → Modules → ShipBob → <account>**. Tabs: Workflows, Permissions.

## Workflows

Reads:

- **Get Order Info** — by ShipBob order ID or reference ID.
- **Get Shipment Timeline** — per shipment.

Writes:

- **Cancel Order** — the whole order.
- **Cancel Shipment** — one shipment within an order.
- **Create Replacement Order** — reference ID, shipping method, order type, recipient and products.
- **Update Shipment Address** — full recipient and address set, per shipment.

ShipBob distinguishes order-level from shipment-level actions; most modules do not.

Data readers: orders, products.

## Settings available in the UI

- **Active**, **Disconnect**.
- **Permissions** — the OAuth scopes granted to Siena. Changing these re-runs the ShipBob authorisation.

There is no name field on this screen.

## Effects outside Siena

Webhook unsubscription happens only on disconnect, not when the integration is disabled.

## Not configurable in the UI

- There is no per-workflow switch on this page; the Workflows tab is read-only. Whether an action runs is set by the automation that calls it.

## Direct link

`https://app.siena.cx/modules/shipbob/<uuid>`

Take `<uuid>` from `integrations[].uuid` on this module's `list_modules` row — one entry per connected account. Name the account when a merchant has more than one. With no UUID to hand, link to the account list: `https://app.siena.cx/modules/shipbob`
