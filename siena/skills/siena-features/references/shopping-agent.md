# Feature: Shopping Agent

Siena's agent for selling: it recommends products, answers catalog questions and
handles pre-purchase conversations. UI: **Shopping Agent**, at `/shopping-agent`.

**To make changes, use the `configuring-the-shopping-agent` skill** with the
`manage_shopping_agent` tool. If that tool is not available, this
organization does not have it: explain and point at the screen rather
than offering to change anything.

## What it is

Not a separate bot. It is the same agent answering a different way: when it has
products to recommend, its answer **replaces** the usual knowledge-base answer
for that message. That is why a merchant sees product cards instead of a policy
answer, and why tuning the knowledge base does not change a shopping reply.

Three things must **all** be true for it to run on a message:

1. The Shopping Agent is switched on for the organization.
2. The channel the message arrived on is switched on in its settings.
3. Siena decides that particular message is a shopping message.

The third is the usual answer to "the Shopping Agent isn't working" — a correctly
configured agent still will not answer "where is my refund" with product cards,
and should not. Check all three in order, first one first: until the
organization is switched on, channel toggles change nothing.

One condition sits upstream of all three: **an organization with no active
knowledge source never reaches the shopping answer at all.** So "all three are
on and it is still silent" is worth checking against Knowledge Sources.

## Two tiers, and why controls go missing

- **Shopping Agent** — channels, agentic features, guidance, review pairings.
- **Shopping Agent 2.0** — adds per-product and per-collection merchandising:
  visibility, recommendation priority, product knowledge, collections.

A base-tier organization has no catalog controls at all. If merchandising is
missing, that is why, and moving to 2.0 is something Siena does.

## The vocabulary the merchant sees

| Dashboard | Means |
|---|---|
| **Active Channels** | Where it may sell: Voice, Social DMs, Social Comments, Live Chat, SMS & WhatsApp, Email, Other, Playground. These are the 2.0 labels; the base-tier screen words them differently and shows Voice only where it is enabled |
| **Conversation Starters** | AI-generated suggestion chips in the chat widget, per product |
| **Buy Button** | Add-to-cart on product cards. **Live Chat widget only** |
| **Review Intelligence** | Review syncing plus AI review summaries |
| **Guidance** | The instruction text it reads on every conversation |
| **Visibility: Visible / Excluded** | Excluded means Siena does not know the product exists |
| **Boost / Default / De-prioritize / Bury** | Bury = never suggested, but still answered about if the customer names it |
| **Include in Shopping Agent** (collection) | Cascades to every product in it |
| **Collection priority** | Boost / Default / De-prioritize. **No Bury** for collections |
| **Product knowledge** | Up to five merchant-authored notes per product |
| **Quality score** | Siena's measure of how well a product's data supports selling it |

## Choosing the right lever

The obvious choice is often wrong:

- "Stop pushing this" → **De-prioritize** or **Bury**. The product stays
  answerable.
- "Pretend it doesn't exist" → **Excluded**. It leaves retrieval entirely, so
  the agent cannot answer questions about it either. Confirm first; this is the
  one that surprises people.
- "Push this" → **Boost**, or boost the whole collection.
- "It recommends the wrong thing generally" → **Guidance**, not per-product
  edits. Guidance is the lever that scales.
- "It says something wrong about this product" → **Product knowledge**.

Collection changes cascade to member products, so a collection is the efficient
lever when the merchant describes a category rather than an item.

Merchants asking to "change what the Shopping Agent is told" almost always mean
**Guidance**. Guidance caps at 7,000 characters and longer text is rejected
rather than truncated.

## What is not a self-serve setting

- **Switching the Shopping Agent on**, and **moving to 2.0** — ask Siena.
- **The agent's own underlying instructions** are not merchant-editable.
  Guidance is the merchant's equivalent, and usually what they actually want.
- **Selling Style.** The consultative / education-first / direct control is not
  saved. Do not offer it.
- **Enabling it on Slack, native Meta DMs or Meta comments.** These have no
  channel setting at all — absent is not "off and could be turned on".

## Honest caveats

- **Store Availability** can be switched on but has no effect.
- **The Collections tab does not save.** Its include/exclude, priority and bulk
  controls change the screen and toast success without persisting. A merchant
  who says they already excluded a collection there is describing something that
  never landed — read the stored state, not the display.
- **The Product Catalog's bulk bar does not save either**, and its Sync button
  starts no sync. Single-product changes on that screen do persist.
- **Conversation starter regeneration is queued**, not immediate, and is
  impossible for a product with no storefront page.
- **Multi-store orgs have one catalog per store** with no safe default — ask
  which store rather than guessing.
- **An org whose Shopping Agent was never set up** cannot be configured at all;
  route to Siena rather than retrying.
- **Review pairings are per Shopify store**, Yotpo and Okendo only. On the 2.0
  screen the store and platform dropdowns do not save — they toast success
  without pairing anything; only the legacy screen actually pairs.
- **Reviews is a real channel with no control on either screen.** The setting
  exists and is honoured, but nothing in the dashboard sets it — so "there is no
  Reviews toggle" is true of the screens, not of the capability.
- **Stock is a quantity, never a state.** Untracked variants show as zero while
  purchasable, oversold ones show negative. Never turn a number into "out of
  stock".

Related: `references/shopify.md` in the `modules-and-integrations` skill for the
store connection, `siena-live-chat.md` next to this file for the widget the Buy Button and starters render in.
