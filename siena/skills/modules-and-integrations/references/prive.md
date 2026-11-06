# Module: Prive

Type: subscription module. UI: **App Store → Modules → Prive → <account>**.

## Workflows

All writes:

- **Cancel Subscription** — takes a cancellation reason from Prive's own reason list.
- **Change Next Shipment Date** — needs the new date.
- **Swap Subscription Product** — original item and SKU, replacement item, SKU, quantity, variant.
- **Update shipping address**
- **Send Order Now** — ships the next order immediately.

Prive's actions have **no intent-check step**. Where Skio, Recharge and Stay Ai validate the request before writing, Prive acts on the instruction directly, so the automation's own instructions carry more weight here.

Data readers: subscriptions, products, customer addresses, cancellation reasons.

## Settings available in the UI

- **Name** — connection display name.
- **Active**, **Disconnect**.
- **Ask for a cancellation reason**, **Confirm before acting**, **Retries**.

## Not configurable in the UI

- There is no per-workflow switch on this page; the Workflows tab is read-only. Whether an action runs is set by the automation that calls it.
- Confidence threshold has no control on this screen.

## Direct link

`https://app.siena.cx/modules/prive/<uuid>`

Take `<uuid>` from `integrations[].uuid` on this module's `list_modules` row — one entry per connected account. Name the account when a merchant has more than one. With no UUID to hand, link to the account list: `https://app.siena.cx/modules/prive`
