# Module: Shopify

Type: commerce module. UI: **App Store → Modules → Shopify → <store>**. Tabs: Workflows, Settings, and Customer Metafields where enabled.

## Workflows

Reads:

- **Get Order Info** — order lookup by order number.
- **Get Discount Code Info** — looks up a discount code.
- **Send Account Activation Link** — returns the customer's account activation URL.

Writes:

- **Cancel Order** — cancels one or more unfulfilled orders. Accepts multiple order numbers.
- **Refund Order** — full, partial or shipping-only. Takes the items, amount, reason and refund level.
- **Edit Order** — add or remove line items, change quantities, edit the note, add or remove tags.
- **Send Replacement** — creates a replacement order. Takes the items, an optional new shipping address, a replacement window, note, tags and discount code.
- **Update shipping address** — on a specific order.
- **Update default shipping address** — on the customer profile.
- **Update email address** — on the customer.
- **Generate Discount Code** — type, amount, prefix, expiry, minimum purchase, maximum shipping price, code length, collections.
- **Create Draft Order** — from an existing order or from line items, with contact and shipping details.
- **Hold Order** / **Release Order Hold** — with a hold reason and notes.
- **Price Protection** — refunds a price difference on an order.
- **Create Customer Metafield** — namespace, key, value and type.
- **File Order Protection Claim** — claim type, description and proof assessment.

Data readers: customer orders, orders with products, customer addresses, customer profile, last order, products, order protection.

## Settings available in the UI

**General** — connection name, store domain.
**Authentication** — client ID, client secret, API key, API secret key.

**Per-action settings**, all on the Settings tab:

| Action | Setting | Default |
|---|---|---|
| Cancel Order | Restock items on cancellation | On |
| Cancel Order | Enforce a cancellation time window | On |
| Cancel Order | Cancellation window, in minutes after the order | 240 |
| Edit Order | Edit window, in minutes after the order | 240 |
| Edit Order | Auto-refund removed line items | Off |
| Update Shipping Address | Address-change window, in minutes after the order | 15 |
| Refund | Allow refunds on unfulfilled orders | Off |
| Send Replacement | Shipping line on the replacement — none, or copy the original | None |
| Send Replacement | Replacement shipping price — free, or same as original | Free |
| Send Replacement | Component bundle metafield — text; empty means off | Off |
| Create Draft Order | Send the draft order invoice | Off |
| All actions | Fulfilment locations — which locations Siena may use | None selected |
| Customer Metafields | Enable each metafield definition individually | — |

The time windows are the reason an action can refuse a request that looks valid: past the window, Siena will not cancel, edit or change the address. If a merchant says Siena refuses to cancel recent orders, check the window before anything else.

Settings save on change; there is no Save button on most cards.

## Not configurable in the UI

- There is no per-workflow switch on this page. The Workflows tab is read-only, and the four clickable rows are informational pages that link back to the Settings tab. Whether an action runs is set by the automation that calls it.
- Confidence threshold and retry count exist but have no control on this screen.
- Order protection beyond the claim policy — pricing and enablement are Siena staff only.

## Direct link

`https://app.siena.cx/modules/shopify/<uuid>`

Take `<uuid>` from `integrations[].uuid` on this module's `list_modules` row — one entry per connected account. Name the account when a merchant has more than one. With no UUID to hand, link to the account list: `https://app.siena.cx/modules/shopify`

The Settings tab anchors each per-action card, so you can link to the exact setting: add `?tab=setting` and append `#order-cancellations`, `#order-edits`, `#shipping-address`, `#order-replacements` or `#draft-orders` — e.g. `https://app.siena.cx/modules/shopify/<uuid>?tab=setting#order-cancellations`. The tab param is required: the Settings panel is not rendered on the default Workflows tab, so an anchor alone scrolls to nothing.
