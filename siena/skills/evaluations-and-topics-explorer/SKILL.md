---
name: evaluations-and-topics-explorer
description: "How to reproduce Siena's Evaluations tabs and Topics Explorer with search_conversations and aggregate_conversations, using the same default filters the screens apply. Use it only when the user asks about those screens specifically — evaluation scores, per-agent evaluation results, trending topics — or compares an answer to what they see there."
---

# Evaluations and Topics Explorer

This covers questions about conversation **evaluations** or **topics** — the same
data shown in Siena's **Evaluations** tabs and **Topics Explorer**. Users compare your
answers to those screens, so you must apply the *same filters the UI applies by default*.
If you skip them, your counts and averages will not match the tab.

All three screens query the **same OpenSearch conversations index** that your
`search_conversations` and `aggregate_conversations` tools query, so you can reproduce the
UI exactly.

### Evaluations › Conversations tab

A paginated list of individual evaluated conversations. Use `search_conversations`.

Default filters:
- `isSpam = false`
- `externalUpdateTime` within the selected range (default preset: the 7 days ending
  **yesterday**; today is excluded)
- `evaluations.<definition>.<field>` `exists = true` for the evaluation in view

Sort: `externalUpdateTime desc`.

### Evaluations › By Agent tab

Per-agent aggregates. Use `aggregate_conversations` with `groupBy: ["participants.name"]`.

Default filters (all enforced):
- `isSpam = false`
- `participants.role = agent`  ← restricts buckets to agent participants
- `externalUpdateTime` within the selected range (default: 7 days ending yesterday)
- `evaluations.<definition>.<field>` `exists = true`

Metrics:
- `total_evaluations` → `count` (the per-agent conversation count shown in the tab)
- For numeric evals (`1_TO_5_SCALE`, `1_TO_100_SCALE`): `avg` of the score field
- For boolean evals (`YES_NO`, `PASS_FAIL`): the UI computes a success rate (count of
  passing ÷ total). Reproduce with a total count plus a count filtered to the passing value.

Group sort: `total_evaluations desc`.

---

### Resolving an evaluation field path (`evaluations.<definition>.<field>`)

Evaluation scores are stored at **`evaluations.<definition>.<field>`**:

- `<definition>` is a stable per-evaluation key (e.g. `conversationQualityScore`). It is
  **not** the human title and **not** derivable by guessing camelCase from the title.
  Guessing is what produces null scores.
- `<field>` is fixed by the evaluation's `assessment_type`:

  | assessment_type   | field       |
  |-------------------|-------------|
  | `1_TO_100_SCALE`  | `score`     |
  | `1_TO_5_SCALE`    | `rating`    |
  | `YES_NO`          | `yesNo`     |
  | `PASS_FAIL`       | `passFail`  |
  | `SELECTION`       | `selection` |

**How to resolve `<definition>` reliably:** no tool returns the `<definition>` key directly,
so sample the index instead of guessing:

1. Run a small `search_conversations` (e.g. `limit` 1–3) with just `isSpam = false` and the
   date range, requesting the `evaluations` field.
2. In a result document, the `evaluations` object is keyed by `<definition>`. Each entry has
   a `title` sub-field. Pick the key whose `title` equals the evaluation the user asked for.
3. Combine with the `<field>` from the table above:
   `evaluations.<definition>.<field>`.

Confirm the path returns non-null values before reporting numbers. If scores come back null,
you used the wrong `<definition>` — re-resolve it; do not silently report nulls.

> Note: the defaults above describe automated (LLM-scored) evaluations. Manually graded
> evaluations behave differently — the tab splits into Graded / Ungraded views, gates on
> whether a human has graded, and Ungraded additionally requires
> `evaluations.<definition>.needsHumanReview exists = true`. If the user is looking at a
> manual evaluation, ask which view they mean before reproducing counts.

---

### Topics Explorer

Topic-level aggregates (no evaluation involved). Use `aggregate_conversations` with
`groupBy: ["subcategory.category"]`.

Default filters:
- `isSpam = false`
- `externalCreateTime` within the selected range (default preset: **Last 30 days**, ending
  today)

Metrics:
- `volume` → `count`
- `avg_cxp` → `avg` of `predictedCxpScore`

Group sort: `volume desc`, then the UI shows the top N (default 10).
