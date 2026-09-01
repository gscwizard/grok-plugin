---
name: weekly-seo-report
description: Produce a repeatable weekly SEO review for one Search Console property from GSC Wizard data — performance vs the prior week, ranking winners and losers, content decay, CTR opportunities, and prioritized actions.
---

# Weekly SEO report

Turn GSC Wizard data into a repeatable weekly review.

## Workflow

1. Identify the requested Search Console property. If ambiguous, call
   `list_sites` and ask the user to choose.
2. Analyze the most recent complete seven-day period (remember GSC data
   settles ~3 days late) and compare with the previous seven-day period.
3. Retrieve, in this order:
   - overall performance: `get_site_summary`
   - biggest ranking winners and losses: `get_ranking_changes`
   - decaying content: `find_decaying_content`
   - CTR opportunities: `analyze_ctr_curve`
   - high-potential opportunities: `score_opportunities`
4. Keep only material findings; drop noise (tiny click counts, position
   moves on near-zero impressions).
5. Produce an executive summary first, then details.
6. Separate observations from recommendations.

## Output template

```markdown
# Weekly SEO Report — <property> — <period> vs <prior period>

## Executive summary

## Performance
- Clicks:
- Impressions:
- CTR:
- Average position:

## Biggest winners

## Biggest losses

## Content decay

## CTR opportunities

## Growth opportunities

## Recommended actions
1.
2.
3.
```

For a client-ready deliverable, offer to run `generate_seo_report`, which
returns a styled, self-contained HTML report.

## Row caps

Results are capped, and a capped list looks exactly like a complete one.
Before calling anything "all" or counting it, check `pagination.hasMore` on
tools that return that block — if true, the list is truncated: either page on
with `offset: <nextOffset>` or say plainly that more exist. The ranked
analyses return the top N by design, so describe them as "the top N by
impact", never as the full set. Full rules in the `gsc-wizard-mcp` skill.
