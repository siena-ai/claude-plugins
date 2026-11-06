# Module: Okendo

Type: review channel. UI: **App Store → Channels → Okendo → <account>**.

## Workflows

None. Okendo is a review channel: Siena moderates and replies to reviews rather than running actions.

## Settings available in the UI

- **Name**, **Website URL**, **Active**.
- **Credentials** — user ID and API key.
- **Review handling**, configured per sentiment (positive, neutral, negative):
  - **Moderation enabled** — whether Siena moderates reviews of that sentiment, plus overall moderation on/off and moderation instructions.
  - **Reply enabled** — whether Siena replies to reviews of that sentiment, each with its own reply instructions.
  - **Selective engagement enabled** — whether Siena chooses which reviews to engage with, each with its own instructions (up to 2000 characters).
- **Reply privately** — reply to the reviewer directly instead of publicly.

Review handling is saved as one block: every field in it is submitted together, so a partial update of one sentiment is not possible.

## Direct link

`https://app.siena.cx/channels/okendo/<uuid>`

Take `<uuid>` from `integrations[].uuid` on this channel's `list_modules` row — one entry per connected account, each with its own `name` and `active` flag. Name the account when a merchant has more than one. With no UUID to hand, link to the account list: `https://app.siena.cx/channels/okendo`
