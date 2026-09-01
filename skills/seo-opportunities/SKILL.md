---
name: seo-opportunities
description: Rank near-term organic growth opportunities for a property using GSC Wizard's impression-weighted opportunity scoring and page-poaching analysis.
---

# SEO opportunity prioritization

## Workflow

1. Resolve the property (`list_sites` if unclear).
2. Run `score_opportunities` — the impression-weighted score favors queries
   sitting just below a ranking threshold, where a small gain yields
   outsized clicks. Use its ranking; do not re-derive scores.
3. Complement with `find_page_poaching_opportunities` for queries just
   outside the top positions, including estimated click upside to a target
   position.
4. Cross-reference the top opportunities:
   - which landing page currently ranks (`query_top_pages` or
     `get_query_performance` with the URL);
   - whether cannibalization is suppressing it (`analyze_cannibalization`);
   - whether CTR is also below par (`analyze_ctr_curve`).
5. Present a single prioritized list (5–15 items), each with:
   - query / page;
   - current position and impressions;
   - estimated click upside;
   - the single highest-leverage action (content improvement, internal
     links, snippet rewrite, consolidation, technical fix).
6. Flag opportunities that are likely brand or navigational queries where
   ranking gains are not realistic — exclude them from the action list.

## Output rules

- Order strictly by estimated impact.
- Every item gets exactly one recommended next action.

## Row caps

Results are capped, and a capped list looks exactly like a complete one.
Before calling anything "all" or counting it, check `pagination.hasMore` on
tools that return that block — if true, the list is truncated: either page on
with `offset: <nextOffset>` or say plainly that more exist. The ranked
analyses return the top N by design, so describe them as "the top N by
impact", never as the full set. Full rules in the `gsc-wizard-mcp` skill.
