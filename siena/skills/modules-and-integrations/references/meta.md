# Module: Meta

Type: channel with workflows (Facebook and Instagram). UI: **App Store → Channels → Meta → <account>**.

## Workflows

All write actions on social content:

- **Hide Comment** — hides a comment.
- **Delete Comment** — deletes a comment.
- **React to Comment** — reacts to a comment.
- **Send Direct Message** — DMs the commenter. Needs the message text.
- **Reply to Direct Message** — replies in an existing DM thread. Needs the reply text.

Data readers: comment context and DM context.

## Journeys

Journeys reach Meta only through their own **Meta Comments** or **Meta DMs** response. There is no fallback to the journey's default response: a journey without one is never matched on that channel, and the comment or DM gets no reply. For how the Social Agent classifies and routes each item, load `Feature: Social Agent`.

## Settings available in the UI

Only two toggles: **Messenger** and **Instagram** — whether Siena handles each source.

The Workflows tab on this page is read-only.

## Not configurable in the UI

- Handoff settings (which helpdesk to hand off to, handoff messages, routing-only sync) are stored for Meta but have no control on the screen. Klaviyo exposes these; Meta does not.

## Direct link

`https://app.siena.cx/channels/meta/<uuid>`

Take `<uuid>` from `integrations[].uuid` on this channel's `list_modules` row — one entry per connected account, each with its own `name` and `active` flag. Name the account when a merchant has more than one. With no UUID to hand, link to the account list: `https://app.siena.cx/channels/meta`
