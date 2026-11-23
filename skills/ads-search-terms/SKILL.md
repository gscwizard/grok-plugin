---
name: ads-search-terms
description: Mine the Google Ads search terms linked to a GSC Wizard property for wasted spend - non-converting terms and n-grams, checked against the converting terms a negative must not block - and return a negative keyword list for the user to add in Google Ads.
---

# Ads search terms

Build a negative keyword list from what people typed before the ads
showed. GSC Wizard never adds negatives itself: the result is a list the
user adds in Google Ads.

## Workflow

1. Resolve the property (`list_sites` if unclear). Call
   `get_google_ads_setup`; when nothing is linked, follow step 2 of the
   google-ads-audit skill first. Use 90 days instead of the default 28 when
   terms get few clicks each.
2. `get_google_ads_search_terms` with `filter: "non_converting"`
   (`limit: 100`): terms with spend and no conversion. Note `coveragePct`:
   the visible terms explain only part of the cost, much less on Shopping
   and Performance Max.
3. The same tool with `ngram: 1`, then `ngram: 2`: each word or word pair
   with the summed metrics of every term containing it. A word with high
   cost and no conversions across many terms (`termCount`) is a stronger
   candidate than any single term. `filter` applies to whole terms only, so
   judge n-grams on their own conversions.
4. For each candidate word, call it again with `filter: "converting"` and
   `contains` set to that word. A converting term containing it would be
   blocked by a broad negative: use phrase or exact instead, or drop it.
5. `get_paid_vs_organic` with `segment: "both_top3"`: terms where the site
   ranks top 3. These belong to a pause test (the paid-vs-organic skill),
   not to the negative list.

## Output rules

- Never propose a brand term or a term that converted in the range.
- Only propose a term when its clicks reach what one conversion takes at
  the account's conversion rate (`get_google_ads_overview`); below that,
  say "too early to judge".
- Match type: broad for a word irrelevant everywhere (shared list), phrase
  for a wasteful word pair, exact for one term whose words also appear in
  converting terms. Campaign level when it is wrong for one campaign only.
  Negatives do not match close variants, so plurals need their own entry.
- One table: negative, match type, level, spend, clicks, terms covered,
  converting terms checked. List the too-early and top-3 organic terms
  separately.
