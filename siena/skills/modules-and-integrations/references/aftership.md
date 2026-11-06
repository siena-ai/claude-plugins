# Module: AfterShip

Type: tracking and returns module. UI: **App Store → Modules → AfterShip → <account>**. Tabs: Settings, Returns, Tracking, Workflows.

## Workflows

Reads:

- **Get Tracking Info** — by tracking number and email.
- **Get Return Status** — by RMA number and email.

Writes:

- **Create Return Link** — order number, email, postal code.
- **Create Return** — order number, email and items. Reads order context from Shopify and the return reasons from AfterShip.

Data reader: return reasons.

## Settings available in the UI

- **Name** — connection display name (Settings tab).
- **API key** — on the Returns tab. Verified against AfterShip on save by listing couriers and returns; an invalid key is rejected.
- **Return reasons** — add and delete the reasons Siena may use when creating a return. This is the one module where the customer manages the action's own option list.
- **OAuth scopes** — re-runs authorisation.
- **Active**, **Disconnect**.

## Not configurable in the UI

- There is no per-workflow switch on this page; the Workflows tab is read-only. Whether an action runs is set by the automation that calls it.

## Direct link

`https://app.siena.cx/modules/aftership/<uuid>`

Take `<uuid>` from `integrations[].uuid` on this module's `list_modules` row — one entry per connected account. Name the account when a merchant has more than one. With no UUID to hand, link to the account list: `https://app.siena.cx/modules/aftership`
