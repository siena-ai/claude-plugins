# Module: Klaviyo

Type: **both channel and module**. UI: **App Store → Channels → Klaviyo → <account>**.

## Workflows

- **Create Event** (write) — records an event on a Klaviyo profile. Needs the metric name, event properties, profile properties, the profile email and a timestamp.

Data reader: Klaviyo integration data.

## Settings available in the UI

- **Active** — enable or disable the integration.
- **Email** and **SMS** — which Klaviyo sources Siena handles.
- **Handoff** — which helpdesk to hand off to, the message shown on handoff, and the message shown when no helpdesk is connected.
- **Sync only routed conversations** — restricts what is synced to conversations Siena routed.

Klaviyo appears in the module list only when the integration is active **and** actions are enabled on it.

## Effects outside Siena

Nothing on update. Webhook removal and token revocation happen only on disconnect.

## Direct link

`https://app.siena.cx/channels/klaviyo/<uuid>`

Take `<uuid>` from `integrations[].uuid` on this channel's `list_modules` row — one entry per connected account, each with its own `name` and `active` flag. Name the account when a merchant has more than one. With no UUID to hand, link to the account list: `https://app.siena.cx/channels/klaviyo`
