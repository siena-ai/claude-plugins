## SOCIAL (META) DOCUMENTS

Social interactions produced by the Social Agent are indexed in the SAME index as helpdesk tickets, with
`integrationName = "meta"` and `externalChannel = "meta"`. They reuse the standard fields wherever possible
and add eight `social*` fields.

### How to identify them

- `integrationName = "meta"`, or
- `channel in ["instagram", "facebook"]`, or
- `socialInteractionType exists` (most precise — only social documents carry it)

### One document per interaction

`socialInteractionType` discriminates the grain — each value is a separate root document:

| socialInteractionType | What the document is | messages/participants | sienaConversationId |
| --- | --- | --- | --- |
| `dm` | One inbound customer DM plus the page-side replies that follow it | yes | when Siena handled it |
| `story_reply` | A DM that replies to one of the brand's stories (`socialParentPostId` = story id) | yes | when Siena handled it |
| `comment` | A whole comment thread, keyed on the top-level comment (replies incl. Siena's are in `messages`) | yes | only when Siena actioned it |
| `post` | The brand's own post — storage-only, no conversation | no | never |
| `mention` | An @-mention signal — no message text | participants only | never |

Consequence: **do not apply the `sienaConversationId exists` filter to social questions.** Use
`conversationsUrl` for links; for social it always points at the Siena `/social` drawer (DM, post and comment
each deep-link to their own drawer), never at the inbox.

### Field semantics on social documents

- `channel` — "instagram" or "facebook" (the platform); there is no separate platform field
- `externalId` — the raw Meta-native id, prefixed by type: `ig_dm_` / `fb_dm_`, `ig_post_` / `fb_post_`,
  `ig_comment_` / `fb_comment_`, `ig_mention_` / `fb_mention_`. Usable with `wildcard`.
- `externalStatus` — engagement/moderation state: "open", "drafted" (Siena reply drafted but unsent),
  "hidden" (comment hidden), "deleted" (withdrawn/removed)
- `subject` / `initialMessage` — post caption for posts, comment text for comments, message text for DMs
  (subject is empty for DMs), mention kind / context url for mentions
- `initialSentiment` and `finalSentiment` are set to the same value (social sentiment is scored once)
- `socialPostType` / `socialCampaignId` are denormalized onto comments from the parent post, so
  "paid ads vs organic" filters work directly on comment documents
- `socialReactionCount` is recomputed live at emit time and always present (including 0) for dm/comment/post,
  so it can go down after an unreact
- `socialAttachmentUrls` is patched in asynchronously after media download — a document may match on text
  before its media urls appear


Analytics consequences:

- Counting "social conversations" means DMs and comments, not posts and mentions — group by
  `socialInteractionType` or filter it explicitly.
- Conversation-quality fields (`resolutionStatus`, `predictedCxpScore`, `escalated`, `totalInteractionCount`,
  `evaluations.*`) are not populated on posts and mentions; averaging them across all social documents silently
  mixes populated and unpopulated documents.
- `initialSentiment` and `finalSentiment` hold the same value on social documents (sentiment is scored once),
  so a sentiment "shift" analysis is meaningless there.
- `socialPostType` / `socialCampaignId` are denormalized onto comments, so paid-vs-organic engagement can be
  aggregated directly on comment documents without joining to the post.
- `externalStatus` carries the moderation/engagement state ("open", "drafted", "hidden", "deleted"), which is
  the field to group by for "how much did we hide / how much is awaiting a reply".
- `socialReactionCount` is recomputed live at emit time (it can go down after an unreact).


### Social examples for search_conversations tool

**All Instagram DMs classified as a real intent, most recent first**:
```json
{
  "fields": ["externalId", "channel", "initialMessage", "socialInteractionIntent", "conversationsUrl", "externalCreateTime"],
  "filters": {
    "and": [
      { "field": "socialInteractionType", "operator": "in", "value": ["dm", "story_reply"] },
      { "field": "channel", "operator": "equals", "value": "instagram" },
      { "field": "socialInteractionIntent", "operator": "equals", "value": "intent" }
    ]
  },
  "sort": [{ "field": "externalCreateTime", "direction": "desc" }],
  "pagination": { "offset": 0, "limit": 25 }
}
```

**Negative comments on paid ads that are still unanswered (no Siena reply drafted or sent)**:
```json
{
  "fields": ["externalId", "subject", "socialCampaignId", "finalSentiment", "socialReactionCount", "conversationsUrl"],
  "filters": {
    "and": [
      { "field": "socialInteractionType", "operator": "equals", "value": "comment" },
      { "field": "socialPostType", "operator": "equals", "value": "ad" },
      { "field": "finalSentiment", "operator": "equals", "value": "negative" },
      { "field": "externalStatus", "operator": "equals", "value": "open" },
      { "not": { "field": "sienaConversationId", "operator": "exists", "value": true } }
    ]
  },
  "sort": [{ "field": "socialReactionCount", "direction": "desc" }]
}
```

**Every comment thread under one specific post**:
```json
{
  "fields": ["externalId", "subject", "socialInteractionIntent", "externalStatus", "conversationsUrl"],
  "filters": {
    "and": [
      { "field": "socialInteractionType", "operator": "equals", "value": "comment" },
      { "field": "socialParentPostId", "operator": "equals", "value": "17851234567890123" }
    ]
  },
  "sort": [{ "field": "externalCreateTime", "direction": "asc" }]
}
```

**Comments that were hidden by moderation**:
```json
{
  "fields": ["externalId", "subject", "socialInteractionIntent", "socialParentPostId", "conversationsUrl"],
  "filters": {
    "and": [
      { "field": "socialInteractionType", "operator": "equals", "value": "comment" },
      { "field": "externalStatus", "operator": "equals", "value": "hidden" }
    ]
  }
}
```

**Social interactions that carry media**:
```json
{
  "fields": ["externalId", "socialInteractionType", "socialAttachmentUrls", "conversationsUrl"],
  "filters": {
    "and": [
      { "field": "socialAttachmentUrls", "operator": "exists", "value": true },
      { "field": "channel", "operator": "in", "value": ["instagram", "facebook"] }
    ]
  },
  "pagination": { "offset": 0, "limit": 20 }
}
```

**Facebook posts, most reacted first**:
```json
{
  "fields": ["externalId", "subject", "socialPostType", "socialReactionCount", "conversationsUrl", "externalCreateTime"],
  "filters": {
    "and": [
      { "field": "socialInteractionType", "operator": "equals", "value": "post" },
      { "field": "channel", "operator": "equals", "value": "facebook" }
    ]
  },
  "sort": [{ "field": "socialReactionCount", "direction": "desc" }],
  "pagination": { "offset": 0, "limit": 10 }
}
```

**Story replies (DMs that answer a brand story)**:
```json
{
  "fields": ["externalId", "initialMessage", "socialParentPostId", "conversationsUrl", "externalCreateTime"],
  "filters": { "field": "socialInteractionType", "operator": "equals", "value": "story_reply" },
  "sort": [{ "field": "externalCreateTime", "direction": "desc" }]
}
```

**@-mentions of the brand**:
```json
{
  "fields": ["externalId", "channel", "subject", "initialMessage", "participants", "externalCreateTime"],
  "filters": { "field": "socialInteractionType", "operator": "equals", "value": "mention" },
  "sort": [{ "field": "externalCreateTime", "direction": "desc" }]
}
```

**Spam / harmful social content needing moderation**:
```json
{
  "fields": ["externalId", "socialInteractionType", "socialInteractionIntent", "subject", "conversationsUrl"],
  "filters": {
    "field": "socialInteractionIntent",
    "operator": "in",
    "value": ["spam", "harmful"]
  },
  "sort": [{ "field": "externalCreateTime", "direction": "desc" }]
}
```



### examples for aggregate_conversations tool

**Volume by social interaction type and platform**:
```json
{
    "groupBy": ["socialInteractionType", "channel"],
    "metrics": [{ "name": "interaction_count", "type": "count" }],
    "filters": { "field": "socialInteractionType", "operator": "exists", "value": true },
    "groupSort": [{ "sortBy": "interaction_count", "order": "desc" }]
}
```

**Intent mix of DMs and comments**:
```json
{
    "groupBy": ["socialInteractionType", "socialInteractionIntent"],
    "metrics": [{ "name": "interaction_count", "type": "count" }],
    "filters": {
      "field": "socialInteractionType",
      "operator": "in",
      "value": ["dm", "story_reply", "comment"]
    },
    "groupSort": [{ "sortBy": "interaction_count", "order": "desc" }]
}
```

**Paid vs organic comment engagement**:
```json
{
    "groupBy": ["socialPostType"],
    "metrics": [
      { "name": "comment_count", "type": "count" },
      { "name": "total_reactions", "type": "sum", "field": "socialReactionCount" },
      { "name": "avg_reactions", "type": "avg", "field": "socialReactionCount" }
    ],
    "filters": { "field": "socialInteractionType", "operator": "equals", "value": "comment" },
    "groupSort": [{ "sortBy": "comment_count", "order": "desc" }]
}
```

**Sentiment of comments on ads**:
```json
{
    "groupBy": ["finalSentiment"],
    "metrics": [{ "name": "comment_count", "type": "count" }],
    "filters": {
      "and": [
        { "field": "socialInteractionType", "operator": "equals", "value": "comment" },
        { "field": "socialPostType", "operator": "equals", "value": "ad" }
      ]
    },
    "groupSort": [{ "sortBy": "comment_count", "order": "desc" }]
}
```

**Moderation outcomes for comments**:
```json
{
    "groupBy": ["externalStatus", "socialInteractionIntent"],
    "metrics": [{ "name": "comment_count", "type": "count" }],
    "filters": { "field": "socialInteractionType", "operator": "equals", "value": "comment" },
    "groupSort": [{ "sortBy": "comment_count", "order": "desc" }]
}
```

**Weekly social DM volume trend**:
```json
{
    "groupBy": [
      { "field": "externalCreateTime", "type": "date_histogram", "interval": "week", "format": "yyyy-MM-dd" }
    ],
    "metrics": [{ "name": "dm_count", "type": "count" }],
    "filters": {
      "and": [
        { "field": "socialInteractionType", "operator": "in", "value": ["dm", "story_reply"] },
        { "field": "externalCreateTime", "operator": "gte", "value": "2025-01-01T00:00:00Z" }
      ]
    }
}
```

**Top ad campaigns by comment volume** (high-cardinality group — always narrow and cap it):
```json
{
    "groupBy": ["socialCampaignId"],
    "metrics": [
      { "name": "comment_count", "type": "count" },
      { "name": "total_reactions", "type": "sum", "field": "socialReactionCount" }
    ],
    "filters": {
      "and": [
        { "field": "socialInteractionType", "operator": "equals", "value": "comment" },
        { "field": "socialPostType", "operator": "equals", "value": "ad" },
        { "field": "externalCreateTime", "operator": "gte", "value": "2025-01-01T00:00:00Z" }
      ]
    },
    "groupSort": [{ "sortBy": "comment_count", "order": "desc" }],
    "bucketLimit": 25
}
```

**Helpdesk-only metrics (excluding social)**:
```json
{
    "groupBy": ["channel"],
    "metrics": [
      { "name": "ticket_count", "type": "count" },
      { "name": "avg_cxp", "type": "avg", "field": "predictedCxpScore" }
    ],
    "filters": {
      "and": [
        { "not": { "field": "socialInteractionType", "operator": "exists", "value": true } },
        { "field": "isSpam", "operator": "equals", "value": false }
      ]
    },
    "groupSort": [{ "sortBy": "ticket_count", "order": "desc" }]
}
```
