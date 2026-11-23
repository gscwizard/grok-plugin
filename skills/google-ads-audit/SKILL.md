---
name: google-ads-audit
description: Audit the Google Ads accounts linked to a GSC Wizard property (link one first if needed) and turn the health score, setup checks, Quality Score, wasted search terms and recent changes into a fix list ranked by the spend each fix affects.
---

# Google Ads audit

GSC Wizard only reads Google Ads. Nothing here changes a bid, budget,
campaign or setting: every fix is one the user makes in Google Ads. Say so
once, near the top of the answer.

## Workflow

1. Resolve the property (`list_sites` if unclear).
2. Call `get_google_ads_setup`.
   - `linked: false`: call `list_google_ads_accounts`. With
     `googleAccounts: 0` the user has not granted Google Ads access yet; tell
     them to open the property in the GSC Wizard app, go to Google Ads
     Overview and use Connect Google Ads. Otherwise list the linkable
     accounts and ask which one to link. Manager accounts cannot be linked;
     the accounts under them can.
   - Only after the user picks one: `link_google_ads_account`, then
     `sync_google_ads` to import the newest months now. A full 16-month
     history usually takes more than one run; the background sync finishes
     it, so audit what is stored and say how far the import got.
   - Report per account any of `lastError`, `historyGaps` (months Google
     kept refusing) and `accessLostAt` (the linking user lost access) before
     the findings: they limit what the audit sees.
3. Gather, for the default last 28 settled days:
   - `get_google_ads_health` (`itemLimit: 5`): the 0-100 score and 13
     checks, each `fail`, `not_checked` or `pass`, with the spend it
     affects and the fix in Google Ads. Give `score.checksRun` with the
     score: a not-checked check counts neither way.
   - `get_google_ads_overview`: spend, conversions and ROAS against the
     previous period, by campaign type and campaign. An impression share
     bound of `below_10` or `above_90` is Google's clamp: write "under 10%"
     or "over 90%", never the number.
   - `get_google_ads_keywords`: Quality Score and its three parts; rerun
     with `belowAverageLandingPageOnly: true` when any landing page
     experience is below average, since that is the part page work can move.
   - `get_google_ads_search_terms` with `filter: "non_converting"`: spend on
     terms that never converted. Report `coveragePct`; Google hides
     low-volume terms.
   - `get_google_ads_change_history`: changes that explain a sharp move.
     The API returns 30 days, so GSC Wizard holds changes from the first
     sync on; an empty list early on means "not stored yet".
4. Build the fix list from the failing checks, ordered by `affectedSpend`,
   with the evidence from step 3 that points at the same campaigns.
5. List the `not_checked` checks separately with what each needs. For paid
   landing pages without an inspection result, offer `bulk_inspect_urls`
   on the most expensive ones (it spends the daily inspection quota; ask
   first).

## Output rules

- Open with the score (and checks run), spend, conversions and ROAS against
  the previous period, and any link or history problem.
- One table: priority, fix, campaign, spend affected, evidence.
- `ctr` and `conversionRate` are already 0-100 percentages; ROAS is a ratio
  (7.2 means 720%). When `currencyConversion` is present, say which
  accounts were converted into the report currency.
- Never call `unlink_google_ads_account` in an audit. Only on an explicit
  request, after warning that it deletes the Google Ads data GSC Wizard
  stored for the property (the Google Ads account itself is untouched).
