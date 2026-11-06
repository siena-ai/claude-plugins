# Module: Google Sheets

Type: data module. UI: **App Store → Modules → Google Sheets → <account>**. Tabs: Spreadsheets, Workflows.

## Workflows

- **Read Sheet** (read) — spreadsheet, sheet name and range.
- **Add Row To Sheet** (write) — appends a row.
- **Update Row in Sheet** (write) — finds the row by a match column and value, then applies the updates.

Data reader: sheets data.

## Settings available in the UI

- **Spreadsheets** — the allow-list of spreadsheets Siena may use. Added through the Google picker, and individually or batch removable. Siena cannot touch a spreadsheet that is not on this list, so an action failing with "no access" usually means the sheet was never added.
- **Refresh** a spreadsheet's metadata.
- **Reconnect** — re-runs Google authorisation.
- **Active**, **Disconnect**.

There is no name field on this screen.

## Not configurable in the UI

- There is no per-workflow switch on this page; the Workflows tab is read-only. Whether an action runs is set by the automation that calls it.

## Direct link

`https://app.siena.cx/modules/google-sheets/<uuid>`

Take `<uuid>` from `integrations[].uuid` on this module's `list_modules` row — one entry per connected account. Name the account when a merchant has more than one. With no UUID to hand, link to the account list: `https://app.siena.cx/modules/google-sheets`
