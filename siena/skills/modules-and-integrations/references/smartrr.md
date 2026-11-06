# Module: Smartrr

Type: subscription module. UI: **App Store → Modules → Smartrr → <account>**.

## Workflows

All writes:

- **Pause Subscription**
- **Cancel Subscription**
- **Restart Subscription**
- **Skip Next Shipment**
- **Update shipping address**

All run an intent check before writing.

Data readers: subscriptions, addresses.

Smartrr has no product swap, frequency change or add-product action. If a merchant asks for those on Smartrr, they do not exist — they are not disabled.

## Settings available in the UI

- **Name** — connection display name.
- **Active**, **Disconnect**.
- **Ask for a cancellation reason**, **Confirm before acting**, **Retries**.

## Not configurable in the UI

- There is no per-workflow switch on this page; the Workflows tab is read-only. Whether an action runs is set by the automation that calls it.
- Confidence threshold has no control on this screen.

## Direct link

`https://app.siena.cx/modules/smartrr/<uuid>`

Take `<uuid>` from `integrations[].uuid` on this module's `list_modules` row — one entry per connected account. Name the account when a merchant has more than one. With no UUID to hand, link to the account list: `https://app.siena.cx/modules/smartrr`
