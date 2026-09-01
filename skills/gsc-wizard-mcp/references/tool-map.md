# GSC Wizard MCP tool map

The full catalog, grouped by what you would reach for it. **106 tools: 76
read, 30 write.** Read the parent [`SKILL.md`](../SKILL.md) first — it has the
rules that matter (raw rows, dates, row caps, scope). This file is the lookup
table for when the common tools do not cover the question.

Every date range is optional; omit it for the last 28 settled days.

## Discovery and account

`list_sites` · `get_account_info` · `list_tags` · `get_inspection_quota` ·
`list_saved_filters`

## Search performance

`get_site_summary` · `query_top_queries` · `query_top_pages` ·
`query_countries` · `query_devices` · `get_query_performance` ·
`get_page_performance` · `get_position_distribution` ·
`get_branded_performance` · `breakdown_by_path` · `get_sitemap_performance` ·
`query_search_analytics` (raw rows — last resort)

Cross-property: `get_cross_site_summary` · `get_tag_group_view`

## Diagnosis

- **Movement:** `get_ranking_changes` · `detect_anomalies` ·
  `detect_change_points` · `list_algo_updates`
- **Decline:** `find_decaying_content` · `get_decay_overview`
- **Competition between your own pages:** `analyze_cannibalization`
- **Under-clicking:** `analyze_ctr_curve`
- **Upside:** `score_opportunities` · `find_page_poaching_opportunities` ·
  `get_longtail_clusters`
- **Demand shape:** `analyze_query_shapes` · `get_query_shape_trend`
- **Data caveats:** `analyze_sampling_impact` (how much GSC is hiding)

## Forecasting and experiments

`forecast_traffic` · `list_experiments` · `get_experiment_results` ·
`compare_migration` · `list_migration_redirects`

## Indexing and technical

`inspect_url` · `bulk_inspect_urls` · `list_url_inspections` ·
`get_indexing_tracker` · `get_indexing_tracker_report` · `list_tracked_urls` ·
`list_sitemaps` · `get_core_web_vitals` · `audit_onpage_seo` ·
`list_indexnow_submissions` · `get_indexnow_settings`

## Grouping

`list_topic_clusters` · `get_topic_cluster_performance` ·
`list_content_groups` · `get_content_group_performance`

## GA4

`list_ga4_properties` · `get_ga4_overview` · `query_ga4_report` ·
`get_ga4_ecommerce` · `get_ga4_key_events` · `get_ga4_llm_traffic` (AI
assistant referrals) · `get_ga4_error_pages`

Joined with Search Console: `get_blended_landing_pages` ·
`get_query_value_attribution`

A GA4 tool returning `notConfigured` means no GA4 property is linked. That is
a state, not an error — report it and move on.

## Bing Webmaster Tools

`list_bing_sites` · `get_bing_traffic_stats` · `get_bing_query_stats` ·
`get_bing_page_stats` · `get_bing_query_page_stats` · `get_bing_crawl_stats` ·
`get_bing_crawl_issues` · `get_bing_link_counts` (backlink counts) ·
`get_bing_keyword_stats` (market demand, no siteUrl) · `get_bing_feeds` ·
`get_bing_url_submission_quota`

Same `notConfigured` rule when no Bing API key is set.

## Reporting and sharing

`generate_seo_report` (styled self-contained HTML) · `list_shared_reports` ·
`list_report_clients` · `list_annotations`

## Write tools — explicit intent only

Never call these without the user asking, in the current conversation. A
read-only key rejects them by design.

- **Annotations:** `create_annotation` · `update_annotation` ·
  `delete_annotation`
- **Properties:** `add_site` · `update_site` · `delete_site` ·
  `create_gsc_property` · `set_dashboard_visibility`
- **Grouping:** `create_topic_cluster` · `update_topic_cluster` ·
  `delete_topic_cluster` · `create_content_group` · `update_content_group` ·
  `delete_content_group` · `manage_tags`
- **Filters and reports:** `create_saved_filter` · `update_saved_filter` ·
  `delete_saved_filter` · `create_shared_report` · `delete_shared_report` ·
  `manage_report_access` · `create_report_client`
- **Indexing:** `submit_indexnow_urls` · `update_indexnow_settings` ·
  `add_tracked_urls` · `remove_tracked_urls` · `check_tracked_url_now` ·
  `bulk_inspect_urls` (consumes daily inspection quota)
- **Migrations:** `add_migration_redirects` · `delete_migration_redirects`

`bulk_inspect_urls` is worth flagging even when a write key is present: it
spends a limited daily quota. Check `get_inspection_quota` first and tell the
user what it will cost.
