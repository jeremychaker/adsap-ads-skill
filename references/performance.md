# Performance check

*"How are my ads doing?", "Weekly report", "What's my ROAS?", "Which creative is winning?"*

## Contents

- What needs attention: recommendations first
- The read chain
- Detail reads
- Metric vocabulary worth reaching for
- Ad preview
- How to end a review
- Worked example

## What needs attention: recommendations first

Start with `adsap_get_recommendations` when the user asks "what needs my attention?" or "what should I fix?". It returns verified findings from Adsap's daily and weekly checks on both platforms, each with evidence numbers and money at risk, plus the latest weekly account summary. The finding types: under-delivering ads, money-losing ads, weak launches, wearing-out ads, wasted Google search terms, keyword promotion candidates. Omit `account_id` for a whole-workspace view; `platform: "meta" | "google"` narrows to one platform. Explain items in plain language from the evidence; suggest actions, never promise results. Say which platforms you covered. A workspace-wide call spans Meta **and** Google, so when one platform returns nothing, state that you checked it and found it clear rather than leaving it out. Silence reads as "not checked", and an advertiser cannot tell the difference.

## The read chain

1. `adsap_get_account_summary`: a **structural snapshot** (campaign, ad set and ad counts by status and objective, budget allocation, disapproved ads, and top spenders over a **fixed last-30-day window**). It takes **no date range**: when the user asked about a specific period, skip it and go straight to `adsap_get_insights`, otherwise you will report a 30-day figure against their question.
2. `adsap_get_insights`: pick the level (account, campaign, adset or ad) and a `date_preset`. Compare against the prior period when the user asks "how is it going". `group_by: "entity"` returns one aggregated row per campaign, ad set or ad across the window, the right shape for "rank my campaigns by spend or ROAS". On claude.ai the insights card has its own rule in `references/cards.md`.
3. `adsap_get_insights_breakdown`: split by age, gender, placement or country. Use it when asked "who or where is it working?".
4. `adsap_get_creative_performance`: creative fatigue (frequency up, CTR down).

## Detail reads

- `adsap_list_ads`: ALL the ads inside an ad set or campaign, live, including ads created today with no delivery yet. Insights views miss those, so this is the only complete ad listing.
- `adsap_get_ad`: ONE ad in full: primary texts, headlines, CTA, destination link, URL parameters, image hashes and video ids, Page and Instagram identity. It also carries the underlying post id that holds the ad's likes and comments. The ids feed straight into the reuse inputs of `adsap_create_ad` and into `adsap_duplicate_ad_set`.
- `adsap_get_ad_set` and `adsap_get_campaign`: one ad set or campaign in full, live (complete targeting spec, placements, attribution, bid strategy, spend limits, schedule, real delivery status, campaign-budget flag and account currency). The cached list tools return null for those fields and their `meta.detail_hint` says so.
- `adsap_get_ad_status`: delivery state of specific ads.
- `adsap_get_account_activities`: "what changed recently?", the audit trail of edits.

## Metric vocabulary worth reaching for

`adsap_get_insights` takes a `metrics` array and its own tool description lists every name. The ones to pick deliberately:

- **`results` and `cost_per_result`**: objective-aware at every level. The ad set's optimization goal decides what counts as a result (purchases, leads, link clicks, ThruPlays, reach), so this is the number the advertiser cares about. Every row carries `result_type` (what a result is; `meta.result_types` maps it to a label such as "Website purchases"), and campaign and account rows resolve to the type their ad sets share. When they mix, `result_type` is `multiple`, `results` is null and `results_breakdown` lists each type separately: never add different result types together. Awareness delivery (Reach, Ad Recall Lift) is a people count fetched live from Meta for the range automatically; for reach, `cost_per_result` is the cost per 1,000 people reached (Meta convention). Both are in the default metric set alongside `link_clicks`.
- **Custom conversions**: the account's own funnel events rather than Meta's standard ones. Get numeric ids from `adsap_list_custom_conversions`, then pass them back as `cc_<id>` (count), `cc_<id>_value` (conversion value) or `cost_per_cc_<id>` (spend per one). The response's `meta.custom_conversion_names` maps ids back to names for the write-up. Google's equivalent split is `adsap_google_get_conversion_breakdown` in `references/google-ads.md`.
- **`outbound_clicks` and `outbound_ctr`**: clicks that LEFT Meta for the advertiser's site. Stricter than `link_clicks`, which also counts clicks to on-Meta destinations. Reach for this when the advertiser says their site analytics show fewer visits than Meta reports clicks.
- **Ad rankings**: `quality_ranking`, `engagement_rate_ranking`, `conversion_rate_ranking` are Meta's own diagnostic labels (`ABOVE_AVERAGE` / `AVERAGE` / `BELOW_AVERAGE_10|20|35`). Ad level only, text not numbers, never averaged or summed. A below-average ranking is the usual explanation for a rising CPM and points at the creative rather than the budget. `null` means Meta has not issued a ranking for that ad yet: say that, do not treat it as average.

## Ad preview

`adsap_get_ad_preview` renders the actual ad, great for "which ad is this?". On claude.ai it arrives as a card with its own rule in `references/cards.md`. An animated clip of the placement is recorded in the background: fetch it about 30 seconds later with `adsap_get_preview_clip` (returns "pending" until ready; free to poll, costs no Meta budget).

## How to end a review

End performance reviews with 2 to 3 concrete recommended actions, and offer to execute them (`references/optimize.md`).

## Worked example

User: "How did my ads do last week?"
Flow: `adsap_list_ad_accounts` (confirm which account), then `adsap_get_account_summary`, then `adsap_get_insights` (`date_preset: "last_7d"`).
Answer shape: headline numbers with the date window stated ("June 28 to July 4: spend €412, ROAS 2.1"). Then 2 to 3 recommendations, then an offer to drill down (breakdown, creative fatigue).
