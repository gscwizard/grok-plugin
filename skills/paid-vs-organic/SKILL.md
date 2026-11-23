---
name: paid-vs-organic
description: Compare Google Ads with organic search for a GSC Wizard property - brand bidding, the paid spend organic results would likely replace, the terms SEO should take over from paid, and landing pages that pay for traffic without any organic presence.
---

# Paid vs organic

Answer three questions: what the paid and organic overlap costs, whether
brand bidding earns its money, and where SEO should take over from paid.
GSC Wizard only reads Google Ads; any change is the user's to make there.

## Workflow

1. Resolve the property (`list_sites` if unclear). Call
   `get_google_ads_setup`; when nothing is linked, follow step 2 of the
   google-ads-audit skill first.
2. Check the brand keywords. The Ads tools return how many there are
   (`brandKeywords` or `brand`). With none, every term counts as non-brand:
   say so, and offer to add them with `update_site` (ask first).
   `get_branded_performance` shows the top branded queries; flag a generic
   word saved as a brand keyword, since it moves generic spend into the
   brand figures.
3. `get_replaceable_spend`: each paid term joined to the same Search
   Console query, its cost weighted by the share of ad clicks that are not
   incremental at that organic position. The weights are a research prior,
   Google's 2012 meta-analysis of 390 ad pause studies: about 50% of ad
   clicks are incremental at organic position 1, 82% at 2 to 4, 96% at 5
   to 10, and 99% to 100% with no organic result on page one.
4. `get_paid_vs_organic`: the segments. `both_top3` is spend where the site
   already ranks top 3 (pause test candidates, brand first); `both_4_20` is
   the SEO priority list, terms paid proves convert while organic sits on
   page one or two; `paid_only` is a content gap; `organic_not_read` means
   Search Console was read up to its row cap, so never count those as gaps.
   Rerun with `segment: "both_4_20"` for the priority list itself.
5. `get_google_ads_landing_pages` with `noOrganicOnly: true`: pages with
   paid clicks and no organic impressions. Add `get_search_scorecard` for
   pages whose paid visitors convert far worse than organic ones (GA4 is
   read live from the property's quota; pass `ga4: false` to skip it).

## Output rules

- The replaceable spend is an estimate at a study average, not a
  measurement of this account: one advertiser can sit far from the average
  either way, and only a pause or geo test measures it. Say this every time
  you give the figure.
- Report coverage before conclusions:
  `coverage.joinedShareOfCampaignCostPct` and `organicNotReadCost`. A
  missing match is not proof of no overlap.
- Brand bidding can still pay for itself (competitors on the brand,
  sitelinks, promotions): present the estimate as the question to test.
- Two tables: brand terms by replaceable cost, and the `both_4_20`
  priority list. Then at most five actions, such as a two to four week
  pause test on the top brand terms in one market.
