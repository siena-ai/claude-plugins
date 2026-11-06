# Module: Recharge

Type: subscription module. UI: **App Store → Modules → Recharge → <account>**.

## Workflows

All writes:

- **Cancel Subscription** — takes a cancellation reason and an optional comment, chosen from Recharge's own reason list.
- **Restart Subscription**
- **Skip Next Shipment** — needs the skip period.
- **Swap Subscription Product** — original item and SKU, replacement item, SKU, quantity, variant.
- **Update Subscription Frequency** — interval and unit.
- **Change Next Order Date** — needs the new date.
- **Update shipping address**
- **Update email address**

Each of these except Change Next Order Date runs an intent check before writing.

Data readers: subscriptions, addresses, products, cancellation reasons.

## Settings available in the UI

- **Name** — connection display name.
- **API client secret** — stored as given; it is not verified on save, so a wrong value fails later at call time rather than at save time.
- **Active**, **Disconnect**.

Recharge does not show the confirmation, cancellation-reason or retry controls that other subscription modules show.

## Not configurable in the UI

- There is no per-workflow switch on this page; the Workflows tab is read-only. Whether an action runs is set by the automation that calls it.
- Confidence threshold and retry count have no control on this screen.

## Direct link

`https://app.siena.cx/modules/recharge/<uuid>`

Take `<uuid>` from `integrations[].uuid` on this module's `list_modules` row — one entry per connected account. Name the account when a merchant has more than one. With no UUID to hand, link to the account list: `https://app.siena.cx/modules/recharge`
