---
name: ctr-opportunities
description: Find queries and pages with lower CTR than expected for their ranking position using GSC Wizard's CTR-curve benchmark, and prioritize the largest absolute click opportunities.
---

# CTR opportunities

## Workflow

1. Resolve the property (`list_sites` if unclear).
2. Run `analyze_ctr_curve`. It compares actual CTR by position bucket (1–20)
   against the AWR industry benchmark server-side.
3. Identify position buckets where observed CTR underperforms the benchmark,
   then drill into the queries/pages driving those buckets with
   `query_top_queries` / `query_top_pages` filtered to meaningful
   impression volume.
4. Prioritize by **absolute click opportunity**: impressions × (expected
   CTR − actual CTR). A small CTR gap on huge impressions beats a large gap
   on tiny volume.
5. For each priority item, suggest snippet/SERP-oriented improvements:
   title and meta description intent match, structured data eligibility,
   date freshness signals, brand clarity. Note when a low CTR is likely
   explained by SERP features (ads, AI overviews, featured snippets) rather
   than the snippet itself — that changes the recommendation.
6. Where relevant, pair with `find_page_poaching_opportunities` for queries
   just outside the top positions with estimated click upside.

## Output rules

- Report expected vs actual CTR and the estimated click upside per item.
- Recommendations must be specific to the page/query, not generic advice.

## Row caps

Results are capped, and a capped list looks exactly like a complete one.
Before calling anything "all" or counting it, check `pagination.hasMore` on
tools that return that block — if true, the list is truncated: either page on
with `offset: <nextOffset>` or say plainly that more exist. The ranked
analyses return the top N by design, so describe them as "the top N by
impact", never as the full set. Full rules in the `gsc-wizard-mcp` skill.
