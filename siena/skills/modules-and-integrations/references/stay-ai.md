# Module: Stay Ai

Type: subscription module. UI: **App Store → Modules → Stay Ai → <account>**.

## Workflows

All writes:

- **Cancel Subscription**
- **Pause Subscription** — needs a date to pause until. Unlike Skio and Smartrr, this pause is bounded.
- **Skip Next Shipment**
- **Swap Subscription Product** — original item and SKU, replacement item, SKU, quantity, variant.
- **Update Subscription Frequency** — interval and unit.
- **Add Product** — item name, SKU, quantity, variant, and whether it is one-time.
- **Update Shipping Address**

All run an intent check before writing.

Data readers: subscriptions, addresses, products.

## Settings available in the UI

- **Name** — connection display name.
- **Active**, **Disconnect**.
- **Ask for a cancellation reason**, **Confirm before acting**, **Retries**.

## Not configurable in the UI

- There is no per-workflow switch on this page; the Workflows tab is read-only. Whether an action runs is set by the automation that calls it.
- Confidence threshold has no control on this screen.

## Direct link

`https://app.siena.cx/modules/stayai/<uuid>`

Take `<uuid>` from `integrations[].uuid` on this module's `list_modules` row — one entry per connected account. Name the account when a merchant has more than one. With no UUID to hand, link to the account list: `https://app.siena.cx/modules/stayai`
