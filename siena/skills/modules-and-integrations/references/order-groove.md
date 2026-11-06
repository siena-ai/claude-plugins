# Module: Order Groove

Type: subscription module. UI: **App Store → Modules → Order Groove → <account>**.

## Workflows

All writes:

- **Cancel Subscription**
- **Restart Subscription**
- **Update Subscription Frequency** — interval and unit.
- **Update shipping address** — takes the fuller address set Order Groove requires, including company name and fax.

All run an intent check before writing.

Data readers: subscriptions, addresses.

No skip-shipment, swap or add-product action exists for Order Groove.

## Settings available in the UI

- **Name** — connection display name.
- **Active**, **Disconnect**.
- **Ask for a cancellation reason**, **Confirm before acting**, **Retries**.

## Not configurable in the UI

- There is no per-workflow switch on this page; the Workflows tab is read-only. Whether an action runs is set by the automation that calls it.
- Confidence threshold has no control on this screen.

## Direct link

`https://app.siena.cx/modules/order-groove/<uuid>`

Take `<uuid>` from `integrations[].uuid` on this module's `list_modules` row — one entry per connected account. Name the account when a merchant has more than one. With no UUID to hand, link to the account list: `https://app.siena.cx/modules/order-groove`
