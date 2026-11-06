# Module: Siena Live Chat

Type: channel (Siena's own chat widget). UI: **App Store → Channels → Siena Chat → <account>**.

## Workflows

None of its own. Actions come from the commerce modules connected to the organization.

## Settings available in the UI

The widest configuration surface of any integration — roughly seventy settings, grouped on the settings screen:

- **Branding** — logo, brand colour, heading, intro message, font, avatar visibility, entry icon, dark mode.
- **Placement** — desktop position, separate mobile position, edge offsets, mobile breakpoint, visibility on mobile and desktop independently.
- **Handoff** — which helpdesk to hand off to, and the messages shown when handoff happens, when no helpdesk is connected, and inside versus outside business hours.
- **Commerce** — connected store, buy button, price display, product image fit, discount code.
- **Visitor capture** — email capture and name capture, each independently enabled and optionally mandatory, with their own heading and description.
- **CSAT** — enable collection, rating question, symbol, whether feedback text is collected, cooldown between requests.
- **AI disclosure** — enable, text, placement, and whether the visitor must acknowledge it.
- **Search and starters** — embedded search, search bar on desktop and mobile separately, starters carousel, fixed conversation starters, pre-generated questions.
- **Other** — privacy policy URL, input placeholder, chat button text, two-way sync, manual install, agent name display, session expiry, ticket subject instructions.

Separate sub-screens, same permission: **pre-generated questions** and **proactive campaigns** (create, update, reorder).

## Direct link

`https://app.siena.cx/channels/siena-chat/<uuid>`

Take `<uuid>` from `integrations[].uuid` on this channel's `list_modules` row — one entry per connected account, each with its own `name` and `active` flag. Name the account when a merchant has more than one. With no UUID to hand, link to the account list: `https://app.siena.cx/channels/siena-chat`
