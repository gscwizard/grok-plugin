---
name: cannibalization
description: Find and triage query cannibalization (multiple pages competing for one query) with GSC Wizard's server-side entropy-scored analysis, and recommend consolidate/differentiate/link/leave-alone actions.
---

# Cannibalization analysis

## Workflow

1. Resolve the property (`list_sites` if unclear).
2. Run `analyze_cannibalization`. It scores queries where 2+ pages compete
   by impression-split entropy — do not recompute this from raw rows.
3. Filter weak or noisy cases: ignore queries with trivial impressions or
   where a secondary page gets a negligible share.
4. For each remaining query, identify the competing URLs and compare their
   relevance and performance (position, CTR, clicks). Use
   `get_page_performance` on the competitors when a trend matters.
5. Recommend exactly one action category per case:
   - **consolidate** — merge/redirect when pages target the same intent;
   - **differentiate intent** — re-scope content when intents genuinely differ;
   - **improve internal linking** — signal the preferred page;
   - **canonical / indexation investigation** — when duplication looks
     technical (verify with `inspect_url` on the competing URLs);
   - **leave unchanged** — when the split is benign.

## Important

Not every multi-URL query is harmful cannibalization. Two pages ranking for
one query can be healthy (double SERP presence). Only flag cases where the
split plausibly suppresses the better page's ranking or dilutes clicks.

## Row caps

Results are capped, and a capped list looks exactly like a complete one.
Before calling anything "all" or counting it, check `pagination.hasMore` on
tools that return that block — if true, the list is truncated: either page on
with `offset: <nextOffset>` or say plainly that more exist. The ranked
analyses return the top N by design, so describe them as "the top N by
impact", never as the full set. Full rules in the `gsc-wizard-mcp` skill.
