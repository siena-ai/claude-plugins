# Module: Slack

Type: channel. UI: **App Store → Channels → Slack → <workspace>**.

## Workflows

One, shared by all helpdesks: **Add tag to ticket** (write). Needs the tags to add.

## Settings available in the UI

**Connection** — Active toggle, Disconnect.

**Agent identity** — display name (up to 80 characters) and avatar. This is how Siena appears when replying in Slack.

**Monitored channels** — a table of Slack channels, each with a **detection mode**:

- **Smart detection** — AI decides which messages become inbox conversations. Marketing blasts, bot notifications and acknowledgments are filtered out.
- **Emoji trigger** — a conversation is created only when a teammate reacts with 🎫. Nothing is captured automatically.
- **All messages** — every customer message becomes a conversation. No filtering.

**Auto-add new channels by prefix** — when a new Slack Connect channel matching the prefix (e.g. `support-`) is created, Siena starts monitoring it automatically in the chosen mode.

**Inbox conversation creation settings** — apply to every monitored channel:

- **Noise reduction** (Smart detection only) — a classifier runs before the AI step and marks clearly no-response messages as Done. Conversations are still created, so nothing is lost. Optionally **apply labels** to those conversations so they can be filtered later.
- **Own-team message exclusion** — messages from the customer's own team do not create conversations. On by default. Turning it off is how a merchant tests the flow from their own workspace.
- **Grouping window** — in non-threaded channels, consecutive messages from the same person within this window become one conversation. Options: 2, 5, 10, 15, 30 minutes. Threaded channels are always one thread per conversation.

**Triage channel** — post conversation summaries to an internal Slack channel on new conversations and on reopened conversations, each event toggled separately.

## Direct link

`https://app.siena.cx/channels/slack/<uuid>`

Take `<uuid>` from `integrations[].uuid` on this channel's `list_modules` row — one entry per connected account, each with its own `name` and `active` flag. Name the account when a merchant has more than one. With no UUID to hand, link to the account list: `https://app.siena.cx/channels/slack`
