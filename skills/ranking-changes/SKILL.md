---
name: ranking-changes
description: Explain what gained or lost rankings and why organic traffic moved, using GSC Wizard's ranking-change analysis. Use for "what lost rankings", "why did traffic fall", "which queries moved".
---

# Ranking changes

## Workflow

1. Resolve the property (`list_sites` if unclear) and the comparison window.
   Default: last 28 settled days vs the prior 28 days.
2. Run `get_ranking_changes` for queries; run it again for pages when the
   user cares about landing pages or when query losses cluster on few URLs.
3. Sort findings by traffic impact (clicks lost or gained), not by raw
   position movement alone. A drop from 2 → 5 on a high-impression query
   outweighs a drop from 45 → 80.
4. Group related queries where appropriate (same topic or same landing page).
5. Distinguish these cases explicitly:
   - rank loss with real traffic impact;
   - rank gain;
   - demand/impression change without a major position change (seasonality
     or interest shift — confirm with `get_query_performance` daily series);
   - CTR change at stable position (hand off to the CTR analysis skill).
6. For losses concentrated in one site section, confirm with
   `breakdown_by_path`. For a suspected specific start date, confirm with
   `detect_change_points` and check `list_algo_updates` for a coinciding
   confirmed Google update.
7. Recommend which pages need investigation, with the evidence for each.

## Output rules

- State both periods explicitly.
- Facts first, interpretation second, recommendations last.
- Never present a correlation with an algorithm update as proven causation.

## Row caps

Results are capped, and a capped list looks exactly like a complete one.
Before calling anything "all" or counting it, check `pagination.hasMore` on
tools that return that block — if true, the list is truncated: either page on
with `offset: <nextOffset>` or say plainly that more exist. The ranked
analyses return the top N by design, so describe them as "the top N by
impact", never as the full set. Full rules in the `gsc-wizard-mcp` skill.
