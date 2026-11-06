# Module: Wonderment

Type: shipment-tracking module. UI: **App Store → Modules → Wonderment → <account>**.

## Workflows

**None.** Wonderment has no first-party actions and contributes no workflows or data readers. It will not appear in a workflow listing.

## Settings available in the UI

- **Name** — connection display name.
- **Active**, **Disconnect**.
- **Ask for a cancellation reason**, **Confirm before acting**, **Retries** — where shown.

## Not configurable in the UI

- Confidence threshold has no control on this screen.

## Direct link

`https://app.siena.cx/modules/wonderment/<uuid>`

Take `<uuid>` from `integrations[].uuid` on this module's `list_modules` row — one entry per connected account. Name the account when a merchant has more than one. With no UUID to hand, link to the account list: `https://app.siena.cx/modules/wonderment`
