# Feature: Social Agent

Siena's agent for social media: it reads comments and DMs on the brand's own
Facebook and Instagram accounts, classifies each one, then replies, moderates,
drafts for a human, or stands down.

UI: **Social Agent** at `/social`. Tabs Hub, Activity, Settings. The
"Early Access" badge shows only to organizations that do not have the feature.

For querying social data use the `data-analytics` skill
(`references/social-agent-data.md`); for the Meta connection and its journey
actions, `references/meta.md` in the `modules-and-integrations` skill. This skill is what the
agent does to an incoming comment, and what the merchant controls.

## What it is, and is not

- **Not the Support Agent with a social skin.** It classifies independently and
  has its own settings per page. One exception that shows up on invoices: an
  item classified **intent** is answered by the Support Agent and billed as
  support, while chatter and moderation bill as social. Friend tags are not
  billed at all, and neither are stand-downs or a human's own manual actions.
- **Facebook and Instagram only.** No TikTok, X, LinkedIn or YouTube ingestion —
  those names do appear as dead labels across several social screens, and are
  not connectors.
- It acts on the brand's **own** accounts. One connected page or account = one
  settings set.

## The five classifications

Every inbound comment and DM gets one. Everything that follows depends on it.

| Classification | Meaning | What happens |
|---|---|---|
| **Intent** | A real request — order status, sizing, returns | Handed to the Support Agent and the matched journey. Skips the engagement roll |
| **Chatter** | Engagement with no specific request | Rolled against the engagement percentage; replied to, drafted, or handed to the losing-roll action |
| **Spam** | Promotional or junk | The channel's moderation rule decides |
| **Harmful** | Abusive or unsafe | The channel's moderation rule decides |
| **Friend tag** | Tagging a friend ("@ana look at this") | Automatically marked done |

Sentiment is scored at the same time, and **positive and negative chatter are
configured separately** — the thing merchants most often miss. Turning up
engagement on positive chatter does nothing to how negative comments are
handled.

## The engagement gate

Chatter is answered **probabilistically, on purpose**: a brand that replies to
every "😍" reads as automated. The merchant sets what share to engage with, and
each comment is an independent roll.

- Set per channel as "Engage with % of positive chatter" and the negative
  equivalent.
- **Intent is never rolled.** A real question is always handled.
- Comments losing the roll are stood down and are not attributed to Siena — so
  they are not agent activity and will not appear as replies.
- For negative chatter only, the losing share gets a **"Remaining N%"** action:
  Mark as done, Hide, Delete, or Flag for review. On DMs hide and delete are not
  offered and it is forced to Flag for review.

Two caveats: **do not quote a single default engagement rate** — a stored
setting defaults to 25%, an organization with no settings at all falls back to
70%, and an unsaved screen shows 70% positive / 0% negative. Three different
numbers, so read the merchant's actual setting. And each comment is its own
roll, so 50% followed by four replies in a row is not a bug.

## Two gates that override everything

- **Response mode: Skip Response** (Intents card). Siena still classifies but
  answers nothing on that channel. The most common reason a correctly configured
  page is silent — check it before touching journeys. The alternative,
  **Journey-Level Control**, defers to each matched journey's own settings.
- **Priority review for influencers** (**Instagram DMs only** — Messenger is
  never influencer-gated). A DM from an account at or above the follower
  threshold suppresses *all* automation and flags for a human, whatever the
  classification. Default threshold 100,000 followers, ships off. Accounts whose
  follower count could not be read are allowed through rather than held.

Two smaller ones: Siena can decline to answer even after winning the roll, which
clears the item deliberately rather than failing; and a **story mention carrying
intent skips the engagement roll** — but one classified as chatter still rolls,
so "story mentions are always drafted" is too strong.

## Moderation

Three rules, configured independently per channel:

| Rule | How it decides |
|---|---|
| **Spam** | Siena's classification |
| **Harmful** | Siena's classification |
| **Keywords** | Exact substring match, case-insensitive, and checked **before** anything else |

Comments offer Automatically hide, Automatically delete and Manual review.
**DMs are locked to Automatically clear** — a DM cannot be hidden, deleted or
held for review; that action control is read-only, though the rule's own on/off
switch still works. All three rules ship enabled, Manual review on comments and
Clear on DMs.

**A disabled rule still classifies.** Switching Spam off does not make Siena
blind to spam; it stands the item down for a human instead of acting. Never say
disabling a rule makes the agent ignore that content.

**Reactions are not a moderation setting.** React to Comment exists only as a
journey action, alongside Hide Comment, Delete Comment, Send Direct Message and
Reply to Direct Message. There is no merchant toggle.

## Meta's limits, which look like Siena bugs

**24 hours** to reply to a DM after the customer's last message — the composer
locks there and the thread is handed to the default team. **7 days** for a
private reply to a comment, which is also when any reply closes out.
**Exactly one private reply per comment**: repeated duplicate attempts feed
Meta's page-wide integrity block, so Siena sends one and only one.

When a merchant reports "Siena didn't reply to this DM", check the window first.

## What the merchant configures

All at `/social?tab=settings`, **per page and per channel tab** — DMs, Organic
Comments, Paid Comments. Paid and organic are split automatically from Meta's ad
flag because ad comments usually warrant different handling.

Moderation (the three rules, each with an action, plus a keyword list) ·
Influencer Handling (DMs only) · Chatter (persona, both engagement sliders,
Response mode Draft or Respond per sentiment, the negative Remaining % action,
and a vibe: Heartfelt, Playful, Bold or Custom) · Friend Tags (one checkbox,
which does not save — see below) · Intents (Response mode) · Copy to all pages.

## Journeys on social channels

Journey-side the channel groups are **Meta Comments** and **Meta DMs**, and
**social is opt-in per journey**. A journey answers an intent comment or DM
only if it has its own Meta Comments or Meta DMs response. **There is no
fallback to the journey's default response**: a journey without one is never
matched on that channel, and the item gets no reply. Never tell a merchant that
a journey with no Meta response will follow its default guidance — to cover it,
add a response for that channel group.

Comments and DMs ingested through a helpdesk (channel groups **Comments** and
**DMs**) are the exception: they do fall back to the default response.

## The /social drawer

Every social item links here rather than to the Inbox, though DM and
story-reply rows also offer an explicit "Open in Inbox". On a comment a merchant
can **Send**, **Edit** or **Discard** a draft, and Reply, DM, Hide/Unhide,
Delete or mark done. Comment tabs are Open, All, Replied, Hidden, Deleted,
filterable by classification. DM threads offer the same send/edit/discard on
drafts.

The Activity tab is the audit list. Its Action column is a single precedence
chain — deleted → hidden → responded → drafted → skipped → cleared → pending —
so an item shows only its strongest outcome. The Inbox's own view still orders
drafted before responded, so the two surfaces can disagree on that pair.

One label to be careful with: **"Approve" in the Activity row menu does not
approve a draft.** It reclassifies the item as chatter. Sending a draft is the
Send button in the drawer.

## What the Siena tools cannot change

**No Siena tool writes Social Agent settings.** Name the
setting, give the path, say what enabling it makes possible — never claim a
change was made.

Also not self-serve: **turning the feature on** (an org without it sees a
lead-capture page); **the comment automation**, switched on separately, so an
org can have the page without comment automation or the reverse — never infer
one from the other; and **the classifier's own instructions**, which are not
merchant-editable.

## Honest caveats

- **The "Hide if negative sentiment" checkbox under Friend Tags saves nothing.**
  It renders, takes a click, and resets on reload. The Copy to all pages dialog
  also lists "Friend tags handling", which does not exist. Offer neither.
- **Intent and influencer settings only save with a channel selected** —
  otherwise they are silently discarded.
- **Stand-downs assign nobody.** An intent held for review stays open and
  unassigned; there is no social team to route to. Negative-chatter "Flag for
  review" is the exception — it resolves the conversation done rather than
  leaving it open for the review it names.
- **Keyword moderation on comments leaves no timeline marker** — it acts before
  the ticket exists, so the trail shows the outcome without the reason.
- **A teammate hiding a comment by hand does not close the ticket.**
- **Post mentions cannot be automated at all.** Story mentions arrive; page and
  post mentions are recorded but never acted on. Absent is not "switched off".
- Follower counts are sparse, which is why influencer handling lets unknowns
  through.

Settings are per page: with several connected accounts, say which one you mean.
Links: `/social`, `/social?tab=settings`, and the Meta connection at
`/channels/meta/<uuid>`.
