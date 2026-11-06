---
name: data-analytics
description: "Methodology and rules for analyzing Siena helpdesk conversation data with search_conversations and aggregate_conversations. Use it before answering any voice-of-customer, data analysis, conversation evaluation, topics or social data question."
---

## Operational Rules (always apply)

- Use aggregate_conversations and search_conversations for queries over all helpdesk conversations. If inbox_lookup is available, use it to read one specific conversation that search_conversations does not return (conversations are indexed 24–48 hours after their last update, so very recent ones may be missing), or when the user explicitly asks for the Inbox.
- By default filter out spam conversations.
- **VERY IMPORTANT**: For aggregate_conversations and search_conversations, always explain the parameters you used, the limitations of the approach, and the results in a clear, objective manner. Make sure the user understands how you translated their request into tool parameters — bullet each field and the chosen filter value.
- **IMPORTANT**: when a search_conversations result is truncated ("[... content truncated due to size ...]"), redo the call with a lower limit, fewer unique IDs, or fewer fields.
- **ALWAYS use the 'fields' parameter in search_conversations** — load only fields directly relevant to the analysis, and always include the ID fields (`sienaConversationId`, `sienaOrganizationId`, `externalId`, plus `conversationUrl` as fallback) so results can be linked.
- Consider the current date when analyzing time-based patterns and making date range queries.

## Question Types & Methodology

**Every query falls into one of three types. Identify which type first.** Hybrid questions ("what are customers saying and how often?") combine types — address each component with its own methodology.

### 1. Discovery Questions — "Find me...", "Show me...", "Which customers/conversations..."

- Goal: locate specific items matching criteria; success is finding relevant items, no minimum count.
- Search until you find relevant matches or exhaust reasonable search strategies.
- **Deliver**: summary of search strategy and parameters, list of items found with key details and direct links, a note if the search appears incomplete or alternative angles might help.

### 2. Qualitative Analysis — "What are customers saying...", "What themes...", "Why are..."

Goal: identify patterns in what/how/why, with customer voice. Use two-phase sampling:

**Phase 1 - Pattern Identification (25-30 conversations):**
- Load minimal fields: `sienaConversationId`, `sienaOrganizationId`, `externalId`, `conversationUrl`, `conversationSummary`, `finalSentiment`, `subcategories`, `externalCreateTime`
- DO NOT load full `messages` (causes context bloat and ID corruption)
- Identify major themes from summaries and validate AI classifications
- Use aggregate_conversations for statistical context (sentiment, volume, trends)

**Phase 2 - Quote Extraction (8-12 conversations):**
- Select representative conversation IDs from Phase 1 and make a targeted search for ONLY those IDs
- NOW load full message content: `messages`, `sienaConversationId`, `sienaOrganizationId`, `externalId`
- Extract verbatim quotes (aim for 2-3 per major theme), building links immediately upon extraction

**Deliver**: data methodology (search parameters, coverage, time period), quantitative context from aggregate_conversations, themes from Phase 1 with supporting quotes from Phase 2, patterns and implications, specific follow-up questions.

### 3. Quantitative Analysis — "What %...", "How many...", "Average...", "Top X..."

**CRITICAL - NEVER DO THIS:**
❌ Calculate percentages, counts, or any metrics from sampled conversations
❌ Report: "From 25 conversations: 18% were size exchanges (13 conversations)"

**ALWAYS DO THIS:**
✅ Use aggregate_conversations for ALL percentages, counts, and metrics
✅ Report: "Across all 2,234 conversations: 18% were size exchanges (402 conversations)"
✅ Use samples ONLY for pattern identification and filter validation

**Phase 1 - Filter Identification (~30 conversations):**
- Load minimal fields to understand the data structure and identify:
  - **Filters**: criteria to narrow data (e.g., sentiment < 0 for negative, specific channels, `initialMessage` when searching for words)
  - **GroupBy dimensions**: fields to split data into buckets (channel, date, category)
  - **Metrics**: what to calculate (count, average, sum, min, max — avg only on numeric fields)
- Validate filters with targeted searches to confirm they return relevant results

**Phase 2 - Aggregate Analysis:**
- Run aggregate_conversations with those filters, groupBy dimensions (group by meaningful dimensions to uncover patterns), and metrics; use dateHistogram for temporal trends relative to the current date
- **CRITICAL**: explain that results are aggregated over ALL helpdesk data, not just the sample

**Deliver**: data methodology (filters, time period, total dataset size), key metrics from aggregate_conversations, statistical breakdowns by segment/category/channel/time, trend analysis where relevant, interpretation of what the numbers mean.

## Search Strategy Protocol

1. **Use Synonym Expansion and OR Logic**
   - Single keywords miss relevant conversations: searching "size" misses "fit," "too small," "runs small," "tight," "loose"
   - Build search with (keyword1 OR keyword2 OR keyword3); always cast a wide net — lean towards more synonyms to find all matching tickets

2. **Apply Appropriate Date Filters**
   - Default to last 90 days for product feedback; extend to 6-12 months for trend analysis; state the time period clearly in your response

3. **Segment by Sentiment First (for negative feedback)**
   - Filter for negative sentiment first, then broaden to neutral if needed — positive conversations often lack critical detail

4. **Use Multiple Search Angles**
   - Don't rely on one query; try different keyword combinations and search by product name, problem type, outcome, channel, timing, escalation patterns

## Quote & Link Integrity (preventing hallucinations)

**Link rules:**
1. ONLY link to conversations you've explicitly loaded via search_conversations
2. Build links immediately when extracting a quote, from the actual returned fields — never later from memory
3. Link priority:
   - `sienaConversationId` exists → `https://app.siena.cx/inbox/?org_uuid={sienaOrganizationId}&conversation_id={sienaConversationId}` (must be UUID format)
   - else `conversationUrl` populated → `[View conversation]({conversationUrl})`
   - else `externalId` exists → note "[External conversation {externalId} - view in your helpdesk]" without a link
4. If an ID looks malformed or unverified, omit the link and note "[link unavailable - ID not verified]"

**Quote rules:**
- For every major finding, aim for 2-3 verbatim customer quotes from Phase 2 conversations. NEVER quote from Phase 1 (summaries only) and never reference a conversation ID you haven't loaded with full messages.
- Preserve exact customer language: emotional expressions ("I'm so frustrated!"), grammatical errors, typos, casual language, non-English phrases, emphasis (capitals, exclamation marks).
- Format for clarity:
```markdown
   > "Exact customer quote here"

   [View conversation](link)
```
- Good quotes demonstrate the pattern, use specific product names/measurements/experiences, show emotion or impact, reveal customer reasoning. Poor quotes: generic statements, paraphrased summaries, agent notes instead of customer words.

**When you cannot extract authentic quotes:**
- Describe patterns from Phase 1 summaries, clearly labeled: "Based on conversation summaries, customers mention [pattern]" — NOT presented as direct quotes
- Acknowledge the limitation; better to acknowledge the gap than fabricate quotes to meet the 2-3 quote target

## AI Classification Validation

**Never trust subcategories, sentiment scores, or AI-assigned classifications blindly:**

1. **Always validate** against actual conversations (summaries in Phase 1, messages in Phase 2) — subcategories are algorithmically assigned and may not reflect customer language; check customers actually say what the category implies
2. **Investigate large uncategorized segments** — if 49% are "General Requests" with no specific reason, dig into what's really happening instead of just reporting the percentage; these often hide important patterns
3. **Provide context for sentiment scores** — sentiment measures conversation tone, not satisfaction with the product/service

**POOR:** "Product Over-Supply is 7% of cancellations (156 conversations)"

**GOOD:** "Product Over-Supply is 7% of cancellations (156 conversations). Examining conversation summaries, this category includes customers mentioning inventory buildup. From 10 detailed conversations, customers explicitly say things like *'I already have an unopened box under my sink'* — confirming the category reflects customer-stated reasons."

## Data Transparency Standards

**At the start of your analysis, include a methodology block:**
```markdown
## Data Collection Approach

**Phase 1 - Pattern Identification:** 25 conversations analyzed (1% of 2,313 total); fields: summaries, sentiment, categories, dates; time period: last 30 days; filters: non-spam, [specific filters]

**Phase 2 - Quote Extraction:** 10 representative conversations with full messages

**Aggregate Analysis:** 2,313 conversations (100% coverage) — used for volume, distribution, trends, percentages

**Data Limitations:** [any relevant caveats]
```

- Always calculate and state the coverage percentage. When qualitative coverage is low (<50%), acknowledge it upfront, explain why (e.g. focusing on most recent, highest-relevance examples), and note the sample identifies major patterns but may miss less common issues.
- **When the data source is wrong for the question**: be honest ("support conversations primarily capture problems, not positive experiences"), explain the gap, suggest alternatives (product review data, repurchase rates, social mentions), and provide what support data does reveal.

## Analysis Depth Requirements

**Surface observation is not sufficient — go beyond cataloging data to interpreting meaning:**

1. **Name significant patterns** — "The Sentiment Paradox," "The Missing 49%" — memorable and actionable
2. **Identify contradictions** — "70% positive sentiment but customers are still leaving" reveals the service-quality vs product-satisfaction distinction; contradictions often reveal deeper insights
3. **Distinguish root causes from symptoms** — not "customers cancel due to product over-supply" but "shipment frequency misaligned with usage rates"; ask what systemic issue creates the symptom
4. **Connect business context** — link findings to policies, processes, systems: "final sale policy amplifies frustration when sizing is inconsistent"; "spike in complaints aligns with sale periods — volume pressure affects QC"
5. **Provide layered analysis** — Layer 1: facts → Layer 2: patterns → Layer 3: implications → Layer 4: questions raised

**POOR (surface):** "Customers are canceling subscriptions. Main reasons: general requests (49%), product over-supply (7%), financial hardship (6%)."

**GOOD (layered):** "2,313 cancellation conversations across 8 categories (facts). Nearly half (49%) classified as 'General Requests' with no specific reason captured (pattern). This uncategorized segment is a missed opportunity to understand why customers leave — and the 74% positive sentiment suggests they leave despite good service (implication). What happens if you analyze timing relative to shipment cycles? Do you follow up 30-60 days later? (questions)"

## Additional Analysis Types

Sentiment trends across channels/time · escalation patterns (when and why) · AI effectiveness (reply success rates, CXP scores) · channel performance (resolution and satisfaction by email/chat/phone) · knowledge base usage · temporal patterns (hour/day/week/month) · volume metrics by dimension · resolution time averages and distributions.

## Tool Notes

- If the user asks about **topics**, use aggregate_conversations / search_conversations with the field `subcategories.category`

## Additional information
- For Social Agent data, or data from the Meta (Facebook/Instagram) integration, read `references/social-agent-data.md`.
