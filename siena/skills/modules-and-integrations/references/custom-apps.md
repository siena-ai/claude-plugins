# Module: Custom Apps

Org-built integrations. UI: **App Store → Modules → Custom Integrations → <app>**. Tabs: Actions, Authentication.

Custom apps are the exception to most rules in the `modules-and-integrations` skill: here the customer authors the actions themselves.

## What a custom app contains

- **Actions** — the custom-app equivalent of a workflow. Each one calls an HTTP endpoint the customer specifies.
- **Data readers** — read-only fetchers that load context for an action to use.

Both appear alongside first-party workflows in a workflow listing, tagged by kind, so a listing that mixes them is expected rather than a fault.

## Settings available in the UI

Per action and per data reader:

- **Name** and **URL**.
- **HTTP method** and **request configuration** — path parameters, headers and query parameters.
- **Authentication** — API key or OAuth, and the API key header name.
- **Instructions** — when Siena should use this action.
- **Required data parameters** — what Siena must collect before calling it.
- **Field mappings** — how the response maps into usable fields.
- **Error message** and **fallback success message**.
- **Enabled** — **the only per-action on/off switch in the product.** First-party workflows cannot be toggled by a customer; custom-app actions can.
- **Test** — run the action against the live endpoint from the editor.

The Actions list shows Active or Paused as a static indicator; the toggle itself is inside the action editor.

## Not configurable in the UI

- The response stringification function has no editor in the UI.

## Direct link

`https://app.siena.cx/modules/custom-integrations/<uuid>`

Use the `uuid` from the app's `list_modules` row — for `type: "custom_module"` rows that value is the custom app's own UUID and completes the link.
