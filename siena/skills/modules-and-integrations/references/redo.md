# Module: Redo

Type: returns module. UI: **App Store → Modules → Redo → <account>**. Tabs: Settings, Workflows.

## Workflows

- **Get Return Status** (read)
- **Send Shipping Label** (read)
- **Cancel Return** (write)

Data reader: returns.

## Settings available in the UI

- **Name** — connection display name.
- **API secret** — write-only; the field clears after saving and the stored value is never shown.
- **Linked Shopify store**.
- **Active**, **Disconnect**.

Store ID is displayed read-only. This screen has explicit **Cancel** and **Save** buttons rather than saving on change.

## Effects outside Siena

Redo has the most active update behaviour of any module:

- Changing the credentials re-verifies them against Redo and re-registers the webhook.
- Turning the integration **on** registers the webhook; turning it **off** unregisters it.

So for Redo, disabling the integration does tear down the third-party webhook — unlike most modules, where that happens only on disconnect.

## Not configurable in the UI

- There is no per-workflow switch on this page; the Workflows tab is read-only. Whether an action runs is set by the automation that calls it.

## Direct link

`https://app.siena.cx/modules/redo/<uuid>`

Take `<uuid>` from `integrations[].uuid` on this module's `list_modules` row — one entry per connected account. Name the account when a merchant has more than one. With no UUID to hand, link to the account list: `https://app.siena.cx/modules/redo`
