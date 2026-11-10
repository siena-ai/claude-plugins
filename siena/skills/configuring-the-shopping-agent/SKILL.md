---
name: configuring-the-shopping-agent
description: "How to configure the Shopping Agent: which channels it sells on, buy button and conversation starters, its guidance text, product visibility and recommendation priority (boost, de-prioritize, bury), and collection merchandising. Use it before answering questions about product recommendations, the shopping agent, or the product catalog."
---

# Configuring the Shopping Agent

Use this alongside the `read_shopping_agent` and `manage_shopping_agent`
tools. It covers what the
Shopping Agent is, the words the customer sees in their dashboard, and the
places where the obvious action is the wrong one.

## What the Shopping Agent is

The Shopping Agent is a branch of Siena's response generation, not a separate
bot. It recommends products, looks up orders and
answers catalog questions. When it finds products, its answer **replaces**
normal knowledge-base retrieval for that message.

Three things must all be true for it to run on a given message:

1. The Shopping Agent is switched on for the organization by Siena.
2. The channel the message arrived on is switched on in the Shopping Agent settings.
3. A per-message classifier decides the message is shopping intent.

That third condition is why a correctly configured agent still will not reply
to "where is my refund" with product cards. If a customer says the agent "isn't
working", check all three before changing settings - and check `get_settings`
first, because it reports whether the agent is switched on at all.

## The vocabulary the customer sees

Use their words, not the column names.

| Dashboard | Tool | Notes |
|---|---|---|
| Active Channels | `support_*` booleans | Voice, Social DMs, Social Comments, Live Chat, SMS & WhatsApp, Email, Other, Playground |
| Conversation Starters | `is_conversation_starter_enabled` | AI-generated chips in the chat widget |
| Buy Button | `is_buy_button_enabled` | Add-to-cart on product cards. Live-chat widget only |
| Review Intelligence | `is_review_summaries_enabled` | Review syncing plus AI review summaries |
| Guidance | `sales_agent_context` | "The instruction set the agent reads on every conversation" |
| Visibility: Visible / Excluded | `tier`: `PRIMARY` / `EXCLUDED` | Excluded means "Siena doesn't know it exists" |
| Recommendation: Boost / Default / De-prioritize / Bury | `boost` | Bury means "never suggested, but still answered about if asked" |
| Include in Shopping Agent | collection `isActive` | Cascades to every member product |
| Collection priority | collection `collectionBoost` | No Bury option for collections |

## Choosing the right lever

This is the distinction that matters most, and the one customers get wrong:

- **"Stop recommending this"** → `DEPRIORITIZE`, or `BURY` if they mean it
  strongly. The product still exists to the agent, so a direct question about
  it is still answered.
- **"Pretend this doesn't exist"** → `EXCLUDED`. This removes the product from
  the retrieval index entirely. The agent can no longer answer questions about
  it at all, including "do you sell X".

Reach for `EXCLUDED` only when the customer really means the second one -
discontinued lines, regional products they cannot ship, items pulled for legal
reasons. Confirm before excluding anything, and confirm before any bulk change.

Collection changes cascade to member products, so "de-prioritize the clearance
collection" is one call rather than fifty. A product with its own explicit
override keeps it; the cascade only moves products that had none.

## Making changes

Through the Siena connector a write lands immediately. There is no Accept/Reject
card like the one in the Siena dashboard.

- Confirm in conversation before anything destructive or bulk: excluding
  products, unpairing reviews, or changing many products at once.
- A change that would change nothing is reported as such. Tell them it is
  already set that way.
- The tool checks everything it can up front - the product exists, the store is
  unambiguous, the review pairing is free - so an error means nothing was
  changed. Fix it and try again.

If the `read_shopping_agent` tool is not available, this organization does
not have the Shopping Agent: explain, and point at the Shopping Agent screen
(`https://app.siena.cx/shopping-agent`) rather than offering to change anything.

## Sequencing

1. Read first. `get_settings`, `get_guidance`, `list_products` and
   `list_collections` are cheap, and describing the current state wrongly is
   worse than asking.
   `list_products` returns one page, ordered most-recommended first, and it
   searches the catalog exactly as the dashboard does - so the customer's
   search and yours agree. For "how many products are excluded?" use the
   catalog totals it returns alongside the page; counting the rows on one page
   understates it.
   For one product, `get_product` returns what the dashboard's product page
   shows apart from its images - description, tags, collections, price range,
   stock by location, variants, brand knowledge and conversation starters - so
   answer detail questions from it rather than from a listing row. Its stock
   numbers are quantities and not availability: a variant with untracked
   inventory syncs as 0 while still being purchasable, and an oversold one
   syncs negative. Quote the number; never turn a total of 0 or less into
   "out of stock".
   `list_collections` pages and searches the store's collections and reports
   how many products each one holds, and how many of those the agent can
   still recommend. `get_collection` returns one collection plus the products
   actually in it - use that, not a `list_products` collection filter, when
   the membership itself is the question: the filter matches the collection's
   title as copied onto each product, so it also has to be spelled the way the
   catalog spells it.
2. `set_guidance` and `set_product_knowledge` both **replace** what is there.
   Read the current value, merge in the change, and send the whole thing.
   Never send just the new sentence.
3. For bulk product changes, list the products first and show the customer what
   you are about to change.
4. Report per-product results honestly. Bulk changes apply one product at a
   time, so a partial failure is a real outcome - say which ones did not apply.

## What you cannot do

Say so plainly instead of implying a change was made:

- **Switching the Shopping Agent on.** Only Siena staff can do that. Until it is
  on, channel toggles change nothing. Tell them to contact Siena.
- **Editing the agent's prompt.** Only Siena staff can change it. Guidance is the
  customer-facing equivalent, and it is usually what they actually want.
- **Selling Style.** Do not offer the consultative / education-first / direct
  slider.
- **Catalog and collection merchandising** is only available to organizations on
  Shopping Agent 2.0. If those actions are missing from your available actions,
  that is why - the organization is not on it, and enabling it needs Siena.

## Honest caveats

- **Store Availability** is not yet active. If they ask for it, set it and say
  it is staged but not yet active.
- **Slack, native Meta DMs and Meta comments** have no channel setting at all,
  so the Shopping Agent cannot be enabled on them. Absent is not the same as
  off - do not tell a customer it is "switched off" and could be turned on.
- **Guidance caps at 7000 characters.** Longer text is rejected before saving.
- **Conversation starter regeneration is queued**, not immediate. Report it as
  queued; do not claim the starters have changed.
- **Conversation follow-ups** need the setting *and* the feature to be available
  for the organization. `get_settings` reports the mismatch when it happens.
- **An organization whose Shopping Agent was never set up** cannot be configured
  at all. The tool reports this rather than silently succeeding; route them to
  Siena support.
- **Multi-store organizations** have one catalog per store, and there is no safe
  default. When the tool lists several, it names each store's
  `shopifyIntegrationUuid` - ask the customer which store they mean and pass
  that uuid, rather than guessing or reporting the action as impossible.
- **Collection changes: use this tool.** When a customer says they already
  excluded or reprioritised a collection, read `list_collections` for the
  stored state rather than taking the dashboard's display as fact, and offer
  to make the change here.
- **Review pairings** are per Shopify store, and a uuid from another
  organization is reported as not found. Read `list_review_pairings` first to
  get the real uuids instead of constructing them.
