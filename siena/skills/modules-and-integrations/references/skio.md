# Module: Skio

Type: subscription module. UI: **App Store → Modules → Skio → <account>**.

## Workflows

All writes, all on a specific subscription:

- **Cancel Subscription**
- **Restart Subscription**
- **Pause Subscription**
- **Skip Next Shipment** — needs the next shipment date.
- **Swap Subscription Product** — original item and SKU, replacement item, SKU, quantity and variant.
- **Update Subscription Frequency** — interval and unit.
- **Add Product** — item name, price, SKU, quantity, variant, and whether it is one-time.
- **Apply Discount** — type, value, number of orders, title.
- **Update Subscription Shipping Address**.

Most of these run an intent check before the write, so a vague or ambiguous request is refused rather than acted on.

Data readers: subscriptions, products.

## Settings available in the UI

- **Name** — connection display name.
- **Discount validity days** — how long a discount Siena applies stays valid. Default 90.
- **Retries** — retry count for failed calls.
- **Webhook token**.
- **Active**, **Disconnect**.
- **Ask for a cancellation reason** and **Confirm before acting** — where shown.

## Not configurable in the UI

- There is no per-workflow switch on this page; the Workflows tab is read-only. Whether an action runs is set by the automation that calls it.
- Confidence threshold has no control on this screen.

## Direct link

`https://app.siena.cx/modules/skio/<uuid>`

Take `<uuid>` from `integrations[].uuid` on this module's `list_modules` row — one entry per connected account. Name the account when a merchant has more than one. With no UUID to hand, link to the account list: `https://app.siena.cx/modules/skio`
