---
name: modules-and-integrations
description: "Load before answering anything about integrations, apps or modules — which ones Siena supports, whether one is connected, what actions it can run, what a customer can configure and where in the UI. Required before telling a user that Siena can or cannot do something that depends on an integration. Has one reference file per app."
---

# Modules and Integrations

## Channels and modules

Two different things, presented in one App Store list.

- A **channel** is where conversations come **in**. It ingests: email, chat, social, reviews. A channel row carries per-account detail — which sources it takes, whether each account is active.
- A **module** is how Siena **does something** in an external system: cancel an order, skip a shipment, adjust points. Only a module can own actions.

Channels produce conversations; modules produce capabilities. Gorgias brings the ticket in, Shopify cancels the order. A merchant needs both to automate anything that changes data.

Three integrations are both, so do not treat the categories as exclusive:

- **Klaviyo** is a channel and a module — it ingests email and SMS, and it has a Create Event action.
- **Yotpo** and **Meta** are channels that also own actions (loyalty points; comment and DM handling).

**Helpdesk** is not an integration and nobody connects it. It is where the shared "Add tag to ticket" action lives, and it applies to whichever helpdesk is connected.

## Vocabulary

- **Custom app** — a module an organization builds itself.
- **Workflow** = **action** = **subworkflow**. The same thing under three names. A workflow is one capability of a module ("Cancel Order", "Skip Next Shipment"). First-party workflows are seeded by Siena staff and are identical across organizations.
- **Data reader** — a read-only fetcher that loads context (orders, subscriptions, addresses) for a workflow to use. Read operations only; never writes.
- **Custom-app actions** are `workflow_functions`; custom-app readers are `data_readers`. Both are org-authored.

## Reading the current state

- `read_automations` operation `list_modules` — which apps exist, which are installed, and per-channel detail on what each ingests. Each row carries `key`, `name`, `type` and `installed`.

  **`type: "helpdesk"` means channel, not helpdesk.** Every channel gets that value — Meta, Slack, Yotpo, Okendo, Klaviyo, Email and Siena Live Chat included. Never tell a user their helpdesk is Yotpo or Slack on the strength of this field; the helpdesks are Gorgias, Zendesk, Intercom, Kustomer, Dixa and Gladly. The other two values are `module` and `custom_module`.
  The output key is `modules` but it holds **every app, channels included, installed or not**. Do not read the key as "these are all modules", and always check `installed` before saying a merchant has something.

  A channel row also carries `channels_status`, and — only when `channels_status` is `ok` — `active`, `enabled_channels` and `integrations[]`. `enabled_channels` is the union over **active** accounts only, so an inactive account contributes nothing. `channels_status: "error"` means the lookup failed — it does not mean the channel ingests nothing, and must never be reported as "no channels enabled".
- `read_automations` operation `list_workflows` — the workflows available per module, each tagged with its `workflow_type` (subworkflow, workflow_function, data_reader).

Installed means a connection row exists. Installed is not the same as active, and active is not the same as configured to do a given thing.

## Rules

1. **Never answer "Siena can't do that" for a capability that depends on configuration without checking first.** Call `list_modules` / `list_workflows`, then read that app's reference file.
2. When a capability is gated on a setting: name the setting, say what becomes possible once it is on, state that changing it is a manual step in the UI, and give the navigation path.
3. **No Siena tool changes module or integration configuration.** Every change below is made by the user in the UI. Do not claim a change was made, and do not offer to make one.
4. Do not name a setting that is not listed in a reference file. Several settings exist in the API with no UI control; presenting those as user-changeable sends the user looking for a control that is not there.

## Facts that hold for every module

- **Whether an action runs is controlled by the automation that calls it, not by the module page.** A workflow executes because an automation has a response pointing at it. To stop an action, remove or change that response, or deactivate the automation — all of which the customer controls, and which `manage_automations` can change. The module page's Workflows tab is a read-only list with no per-workflow switch; custom apps additionally have an enable toggle per action.
- Per-action identity-resolution requirements are set in **Settings → Data Protection**, not on the module page.
- Connecting and disconnecting is an OAuth or credential handshake, not a setting.
- Disabling an integration (`is_active` off) is not the same as disconnecting it. Third-party teardown — webhook removal, token revocation — happens only on disconnect.
- Channel and module settings screens require the Admin or Owner role. Manager and Agent roles do not see them.
- Renaming a connection and rotating credentials are available on most modules; neither changes behaviour.

## Finding the reference file

Each app has one file in `references/`, named after its display name in
kebab-case (`Stay Ai` → `references/stay-ai.md`). Resolve it like this:

- Use the **display name** from the listing, not the key. `list_modules` returns both; the keys (`sienaChat`, `loopReturns`, `googleSheets`, `orderGroove`, `stay_ai`) match no file name. The display name does.
- Anything with `type: "custom_module"` is named by the organization that built it, so its name will never appear in the index. Read `references/custom-apps.md` for all of them.
- `list_workflows` reports a module called **Helpdesk**. It is not an integration — it is where the shared "Add tag to ticket" action lives, and it belongs to whichever helpdesk is connected. There is no reference file for it; read the connected helpdesk's instead.
- Do not guess a file that is not in the index below.
- **An app in the tool output with no reference file in the index below is available only to some organizations, which is why no file exists for it.** Report only what the tool returned — its name, `installed`, and for a channel its `active` and `enabled_channels`. Do not name a setting, an action or a UI path for it, and do not tell the user Siena has no such integration: the tool just told you this organization has it.

**Check state before reading, not instead of reading.** Call `list_modules` first, then read the reference file.

- Installed: describe its settings as available, and give the UI path.
- Not installed: say it is not connected, then use the reference file to say what connecting would make possible. Never describe an uninstalled module's settings as though the merchant can go and find them.
- Not in the catalogue at all: say Siena has no integration with it. Do not describe a substitute as if it were the same thing.

## Building links

The app is at `https://app.siena.cx`. Give the user a link whenever you name a place to go.

| Target | URL |
|---|---|
| One channel account | `/channels/<slug>/<uuid>` |
| All accounts on a channel | `/channels/<slug>` |
| One module account | `/modules/<slug>/<uuid>` |
| All accounts on a module | `/modules/<slug>` |
| A custom app | `/modules/custom-integrations/<uuid>` |
| Identity resolution per action | `/settings/?tab=data_protection` |

The slug is in each reference file's Direct link section. It is not always the app key — camelCase becomes kebab-case and underscores are dropped, so the six that differ are:

| App | Slug |
|---|---|
| Siena Live Chat | `siena-chat` |
| Stay Ai | `stayai` |
| Order Groove | `order-groove` |
| Loop Returns | `loop-returns` |
| Google Sheets | `google-sheets` |
| Custom apps | `custom-integrations` |

Every other app's slug is its key unchanged.

**Every installed app can be deep-linked, given a UUID.** Each row carries `integrations[]`, one entry per connected account with its own `uuid` and `name`; channel rows add `active` and `enabled_channels` per account, and carry `integrations[]` only when `channels_status` is `ok`. That UUID completes the link. An org can hold several accounts on one app — a merchant with multiple Shopify stores — so when there is more than one entry, say which account you mean rather than picking silently. Custom apps use the row's own `uuid` instead.

Never invent a UUID to fill a link. If you do not have one, link to the list page.

## Reference files

Read the one for the app in question.

Channels:
`references/gorgias.md` · `references/zendesk.md` · `references/intercom.md` · `references/kustomer.md` · `references/dixa.md` · `references/gladly.md` · `references/email.md` · `references/siena-live-chat.md` · `references/slack.md` · `references/meta.md` · `references/okendo.md` · `references/yotpo.md` · `references/klaviyo.md`

Modules:
`references/shopify.md` · `references/stripe.md` · `references/recharge.md` · `references/skio.md` · `references/stay-ai.md` · `references/smartrr.md` · `references/order-groove.md` · `references/prive.md` · `references/wonderment.md` · `references/fulfil.md` · `references/loop-returns.md` · `references/shipbob.md` · `references/redo.md` · `references/shipmonk.md` · `references/aftership.md` · `references/google-sheets.md`

Custom apps:
`references/custom-apps.md`
