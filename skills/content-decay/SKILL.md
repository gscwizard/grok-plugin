---
name: content-decay
description: Find pages losing organic traffic over time with GSC Wizard's decay detection, classify the decay type (ranking, demand, CTR, replacement), and prioritize refresh candidates.
---

# Content decay

## Workflow

1. Resolve the property (`list_sites` if unclear).
2. Run `find_decaying_content` (pages mode). It buckets losses vs a baseline
   as severe / moderate / mild server-side — use those buckets.
3. Rank pages by absolute click/impression loss, not percentage alone.
4. For top candidates, check whether average position also declined
   (`get_page_performance` daily series, or `get_decay_overview` for the
   month-by-month matrix view).
5. Classify each decaying page as most likely:
   - **ranking decay** — position worsened, demand stable;
   - **demand decline** — impressions fell at stable position (seasonality
     or shrinking interest);
   - **CTR decline** — position stable, CTR fell (SERP layout or snippet
     competition — cross-check with `analyze_ctr_curve`);
   - **page replacement / cannibalization** — another URL on the same site
     took over (cross-check with `analyze_cannibalization`).
6. Recommend what to investigate or refresh next, ordered by recoverable
   clicks. Content refresh only helps ranking/CTR decay — say so when the
   diagnosis is demand decline.

## Output rules

- State the baseline and comparison windows.
- One diagnosis per page, with the evidence that supports it.

## Row caps

Results are capped, and a capped list looks exactly like a complete one.
Before calling anything "all" or counting it, check `pagination.hasMore` on
tools that return that block — if true, the list is truncated: either page on
with `offset: <nextOffset>` or say plainly that more exist. The ranked
analyses return the top N by design, so describe them as "the top N by
impact", never as the full set. Full rules in the `gsc-wizard-mcp` skill.
