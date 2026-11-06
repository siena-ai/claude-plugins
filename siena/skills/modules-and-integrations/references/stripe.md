# Module: Stripe

Type: commerce module. UI: **App Store → Modules → Stripe → <account>**.

## Workflows

Reads:

- **Get Order Info** — order lookup by order number.
- **Get Order Invoice** — the invoice for an order.

Writes:

- **Cancel Order**
- **Refund Order** — items, amount, shipping refund.
- **Cancel Subscription**
- **Resume Subscription**
- **Update shipping address** — on an order.
- **Update email address**

Data readers: customer orders, subscriptions, addresses.

## Settings available in the UI

- **Name** — connection display name.
- **API key** — rotate the credential from the settings screen. Stripe is the only module with the key field on this shared screen.
- **Active**, **Disconnect**.

## Not configurable in the UI

- There is no per-workflow switch on this page; the Workflows tab is read-only. Whether an action runs is set by the automation that calls it.
- Confidence threshold, retry count and confirmation prompts have no control on this screen.

## Direct link

`https://app.siena.cx/modules/stripe/<uuid>`

Take `<uuid>` from `integrations[].uuid` on this module's `list_modules` row — one entry per connected account. Name the account when a merchant has more than one. With no UUID to hand, link to the account list: `https://app.siena.cx/modules/stripe`
