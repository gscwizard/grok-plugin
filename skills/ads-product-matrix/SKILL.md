---
name: ads-product-matrix
description: Sort the products in Google Shopping and Performance Max ads for a GSC Wizard property into heroes, sidekicks, villains, zombies and below-target products against their campaigns' target ROAS, and add products that spend without converting, never get clicked, or never get a paid impression.
---

# Ads product matrix

Judge every advertised product against the target its campaigns bid for,
and turn the result into product actions. GSC Wizard reads Google Ads and
Merchant Center only; exclusions and targets change in Google Ads.

## Workflow

1. Resolve the property (`list_sites` if unclear). Call
   `get_google_ads_setup`; when nothing is linked, follow step 2 of the
   google-ads-audit skill first.
2. `get_product_matrix` (`limit: 50`): buckets with totals, rules and the
   top products by cost.
   - `hero`: at or above target, inside the small set that brings most of
     the conversion value. `sidekick`: at or above target, outside it.
   - `villain`: enough clicks, and a ROAS far below target or no conversion
     where the conversion rate makes zero very unlikely.
   - `below_target`: under target but not far under.
   - `no_conversions_yet`: no conversion, but an average product could
     still show that. `zombie`: too few clicks to judge.
   - `no_impressions`: in the Merchant Center catalog, no paid impression.
3. Read `judgement` first: the last days of the range, whose conversions
   are still arriving, are left out of the buckets (`excludedFrom`), while
   the totals cover the whole range. `rules` holds the click threshold and
   fallback target; `campaignTargets` each campaign's own target. ROAS is a
   ratio (4 means 400%).
4. Rerun with `bucket: "villain"`, and with `groupBy` brand, category or
   product type on a large catalog to see where villains cluster.
5. `get_google_ads_shopping` with `filter: "no_conversions"` and
   `filter: "no_clicks"` (shown, never clicked: title, image or price).
   With Merchant Center linked, `get_organic_shopping_performance` shows the
   same products in free listings.
6. When `gmc.state` is not linked, Merchant Center attributes are empty and
   `no_impressions` cannot be filled: say so.

## Output rules

- Open with product count, cost and ROAS per bucket, and the concentration
  (`concentration.top80Products` of `concentration.products` bring 80% of
  the value).
- Tables for villains and top heroes: product, cost, clicks, conversions,
  ROAS, target ROAS.
- At most six actions: move or exclude villains, give heroes budget where
  campaigns lose impression share to budget, leave zombies alone until they
  can be judged, fix titles or images of products never clicked.
- When `thresholds.exploratory` is not empty, label the buckets
  exploratory.
