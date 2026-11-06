# Module: Yotpo

Type: review channel with workflows. UI: **App Store → Channels → Yotpo → <account>**.

## Workflows

- **Get Loyalty Data** (read) — the customer's loyalty balance and status.
- **Update Loyalty Points** (write) — adjusts points. Needs the adjustment amount, a history title, whether the entry is visible to the customer, and whether the adjustment applies to points earned.

Data reader: loyalty data.

## Settings available in the UI

Review handling, configured per sentiment (positive, neutral, negative):

- **Review moderation** — overall on/off plus per-sentiment moderation.
- **Review replies** — per-sentiment reply on/off, each with its own instructions.
- **Selective engagement** — per-sentiment on/off, each with its own instructions.
- **Reply privately** — reply to the reviewer directly instead of publicly. On by default; every other toggle here is off by default.

## Direct link

`https://app.siena.cx/channels/yotpo/<uuid>`

Take `<uuid>` from `integrations[].uuid` on this channel's `list_modules` row — one entry per connected account, each with its own `name` and `active` flag. Name the account when a merchant has more than one. With no UUID to hand, link to the account list: `https://app.siena.cx/channels/yotpo`
