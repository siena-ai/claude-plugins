# Feature: Siena Live Chat

Siena's own chat widget: a launcher on the merchant's site that talks to
customers, hands off to a human, and can sell.

UI: **App Store → Channels → Siena Chat → <widget>**, at
`/channels/siena-chat/<uuid>`. Tabs: Customization, Handoff, Campaigns,
Installation, Shopping Agent, plus CSAT where that is enabled.

For the full settings inventory read `references/siena-live-chat.md` in the
`modules-and-integrations` skill. This skill is
the behaviour, and the places where the obvious reading is wrong.

## What it is

The only channel Siena owns end to end — elsewhere it ingests from someone
else's system; here Siena is the widget, the session and the transcript.

An org can have **several widgets**, each with its own settings, install and
key, so say which one you mean. The widget has **no actions of its own**: what
Siena can *do* in a chat comes from the connected commerce apps. A widget goes
live only when it is **active**, installed, and made visible — three separate
switches, all of which have to be right.

## "I installed it and nothing appears"

The most common report, with three distinct causes. In order:

1. **Desktop and mobile visibility both default to off.** A new widget is
   hidden until the Chat Button is switched on for each. Usually this.
2. **The widget is not active** — the switch beside its name.
3. **Both install methods were used at once.** The product warns against it; in
   practice the second install is ignored, so the symptom is usually a stale or
   unexpected widget rather than none at all.

## Installing

Two **mutually exclusive** methods:

- **Shopify 1-click** — pick a connected store and Siena installs the script. It
  needs script-tag permission on the Shopify connection and silently does
  nothing without it.
- **Website snippet** — one `<script>` tag before `</body>`.

The Installation tab warns against using both and requires a confirmation. If a
merchant reports a dead widget on a Shopify store, ask whether the snippet is
also in the theme. Uninstalling clears the method, so they can switch — one at a
time.

Two extra embeds put Siena inside the page rather than in a launcher: the
**Embedded Product Expert** (a `siena-embedded` section in the theme; on a
product page it picks up that product and can add to cart) and a **starters
carousel** element. The Product Expert needs its setting on *and* the container
present; the carousel needs only the container — placing the div is the real
opt-in, whatever the setting says.

## Sessions, and why a conversation "came back"

A session lasts **24 hours** and extends as the visitor keeps chatting. What
happens next surprises people:

- Expires, same widget → new session that **inherits the previous
  conversation**. That is the continuity a returning visitor sees.
- Different widget → a genuinely fresh start, nothing inherited.
- The helpdesk ticket was closed → the session ends early, but the next message
  still inherits the previous conversation, exactly like a normal expiry.
- After a conversation ends there is a 60-minute cooldown; once routed to a
  human the merchant's **Routing Session Expiration** takes over (1 hour to 7
  days).

On expiry Siena decides whether to close the helpdesk ticket from **who spoke
last**, not from whether it was routed: customer last → stays open; human agent
last → closed, even if routed; Siena last → stays open only if routed. An
internal note is left either way.

**Visitors are anonymous** until email or name capture collects something; both
can be enabled independently and made mandatory. Whether a captured email is
merged into the customer's existing Siena profile depends on a per-org setting;
without it the visitor stays a separate anonymous record. Do not promise a chat
will be stitched to a customer's history unless the org has that on.

## Handoff

Four messages, chosen by configuration rather than by the conversation:

| Situation | Message |
|---|---|
| No helpdesk reachable | "No helpdesk" response |
| **Two-way sync**, inside business hours | In-business-hours response |
| **Two-way sync**, outside business hours | Out-of-business-hours response |
| **One-way sync** | Routed response — typically "expect an email" |

Handoff goes to Gorgias, Zendesk, Kustomer, Gladly or Dixa. Three things to be
precise about:

- **Business hours are organization-level, not per widget** (Settings →
  Business Schedule). Editing one widget's out-of-hours message does not define
  when those hours are.
- **Two-way sync is permanent** for that widget. It cannot be returned to
  one-way; undoing it means deleting the instance and reinstalling. With it,
  human replies come back into the chat; with one-way they do not, and the
  ticket carries an internal note saying so.
- **By default only routed conversations create a ticket.** Turning "Handoff
  only routed conversations" off files a ticket for *every* conversation —
  usually what a merchant who "can't find my chats in Gorgias" needs.

Human agents' names are obfuscated per **Agent Name Display** — first name only
by default, or "Agent", or full name. Human agents only. Some orgs also require
an email before routing: the handoff is held, the widget asks, and the ticket is
created once given.

## Selling in chat

The widget is where the Shopping Agent's commerce features render, and this is
where merchants get confused:

**The organization-level Shopping Agent settings win.** The Buy Button and AI
conversation starters are controlled on the Shopping Agent screen, not on the
widget — a widget-level buy button does nothing while the org master is off. The
Shopping Agent tab says so ("Enable on Shopping Agent settings to use this
feature"); point them there.

The widget itself controls whether prices show and how product images are
fitted. A per-widget discount code applied on add-to-cart exists but has no
screen anywhere — do not offer it.

Starters come from two independent places: **fixed starters** (up to six, written
by the merchant, always shown, rendered *before* the AI ones) and
**AI-generated starters**, produced per page URL in the background — a page has
none until Siena has indexed it, and the first visitor to an ungenerated page
triggers generation and may see nothing. They arrive live once ready, so
checking too early shows an empty result that is not a failure. The
widget/carousel/both selector only affects the widget's own list; the carousel's
real switch is whether the div is on the page.

Read `shopping-agent.md` next to this file for what it recommends and how the catalog is
merchandised.

## CSAT and other behaviour

A post-conversation survey with a configurable question, symbol (stars, emojis
or numbers), optional free-text follow-up, and a cooldown — 24 hours by default —
so a conversation is not asked twice. One trap: **an empty rating question
silently suppresses the survey**, so a merchant who cleared the text expecting
the default is getting nothing.

**AI disclosure** sits in the footer, as a header banner, or as the first
message; requiring acknowledgement is unavailable with first-message placement.
**Localization** covers merchant-authored copy per locale, but the widget's own
built-in labels ship in **English and French (Quebec) only**; while editing
French the non-text controls are locked, since they are shared across languages.
**Removing Siena branding is not a setting** — Siena grants it per customer.

## Proactive campaigns

A static message bubble popping above the launcher to start a conversation.

**These are not the Campaigns product.** Outbound Campaigns is separate — bulk
email and SMS at `/campaigns` — and shares nothing with these. Merchants
conflate them constantly; ask which they mean.

- Up to **10 per widget**, each with a message, a page rule and one trigger.
- **Two triggers only**: after N seconds on the page, or after scrolling N% of
  it. No exit intent, page-type, geo, visitor-state or business-hours
  conditions — do not offer those.
- Page rules match the **URL path** with `*` wildcards; exclusions win.
- **Highest-priority match wins, one campaign per pageview.** Dragging up the
  list sets priority.
- A dismissal is remembered for 24 hours; skipped on mobile when the widget is
  centred.
- **The message is static text, not AI**, and showing it creates nothing — a
  conversation exists only if the visitor types. So there is no per-campaign
  impression or click report to read back.

Campaign changes save immediately, unlike the rest of the editor.

## What the Siena tools cannot change

**No Siena tool writes Live Chat settings**, campaigns or
starters. Name the setting and its path; never claim a change was made.

Also not self-serve: branding removal, the widget's language set, and the
sync and backfill controls on the Installation tab. The list of AI-generated
starters is managed for the merchant — they can add fixed starters but cannot
author generated ones.

## Honest caveats

- **The CSAT follow-up threshold control does nothing** and cannot be changed.
  Do not describe it as configurable.
- Toggling "Show next to messages" alone can leave Save disabled; something else
  must change too.
- The floating search bar shows on desktop by default even where the saved
  setting reads as off, so the screen may not match what the merchant sees.
- Settings are per widget — answering as though there is one set is how a
  merchant edits the wrong one.

Links: one widget `/channels/siena-chat/<uuid>`, all widgets
`/channels/siena-chat`, business hours `/settings/?tab=business-schedule`,
Shopping Agent `/shopping-agent`.
