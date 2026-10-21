# Research and strategy

*"What are my competitors running?", "Show me their static ads", "Analyze my website", "Build me a plan"*

- `adsap_search_ad_library`: live ads of ANY advertiser (competitor teardown, creative inspiration). Public data, works without their permission. Coverage is Meta's rule, not Adsap's: ordinary commercial ads are returned only where they reached the EU or UK, so never promise US-only competitor research from it. Filters: `media_type`, `search_type: "KEYWORD_EXACT_PHRASE"` for a brand or product name, `publisher_platforms`, `languages`, `ad_delivery_date_min` and `ad_delivery_date_max`. `media_type` takes `STATIC` for image ads only or `VIDEO` for video only. An unfiltered list mixes both with no marker, so never pick "static" ads out of it by guessing. Each ad carries `eu_total_reach` (people reached across the EU, not per country) and `days_running`: together they show which ads the advertiser is scaling. Meta publishes no spend data here. No Page ID yet? Search the brand name, read `data.pages`, then re-run with that `page_ids`.
- `adsap_get_ad_library_snapshot`: SHOW those ads. Pass up to 8 ids in `ad_ids`; ONE call renders them all. Whenever the user asks to SEE or SHOW ads, call it in the same turn as the search, with the top results: a table of links shows nothing. Always one batched call, never one call per ad. On claude.ai the gallery card has its own rule in `references/cards.md`.
- `adsap_analyze_website`: fetch and analyze a URL (business context, value proposition, tracking installed, pricing signals). A good opener for strategy conversations.
- `adsap_manage_onboarding_plan`: the persisted per-account setup and strategy checklist, so strategy survives across conversations. The plan is found by `ad_account_id` alone: never pass a `plan_id` you were not given by this tool. The 5 standard setup steps (profiles, tracking, naming, launch settings, enhancements) are completed in the Adsap web app; never mark those done from chat. To author a plan: gather context with `adsap_analyze_website` and `adsap_get_account_health` first, then `create` (one plan per account; on ALREADY_EXISTS, read or update instead). On claude.ai the plan card has its own rule in `references/cards.md`.
- `adsap_list_experiments`: read A/B test setups and status (read-only; tests are created in Meta's interface).

## Worked example: competitor ads

User: "Show me the top static ads from <brand> in France."
Flow:

1. `adsap_search_ad_library` with `countries: ["FR"]`, `search_terms: "<brand>"`, `search_type: "KEYWORD_EXACT_PHRASE"`.
2. Read `data.pages` to find the brand's own Page.
3. `adsap_search_ad_library` again with that `page_ids` and `media_type: "STATIC"`, then sort by `eu_total_reach`.
4. `adsap_get_ad_library_snapshot` with the top 8 ids in `ad_ids`, highest reach first, in the same turn.
Answer shape: one short sentence under the gallery (how many static ads are running, and that reach is EU-wide). Offer the reach table or the next 8 only if asked.
