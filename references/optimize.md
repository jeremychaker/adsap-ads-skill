# Optimize and fix what is running

*"Pause that campaign", "Raise the budget", "Swap the creative", "Why was my ad rejected?"*

- `adsap_set_entity_status`: pause or reactivate campaigns, ad sets or ads, up to 50 at once, with a preview.
- `adsap_duplicate_ad_set`: duplicate an ad set into 1 to 10 PAUSED copies KEEPING all its ads (even ones created today). Each recreated ad points at the same creative, so the original post and its likes and comments survive. Per copy you can set a name, a geo override and daily spend limits. The geo override is the geo-split flow: one copy per country or region, keys from `adsap_search_geo_locations`. Spend limits are absolute amounts in cents. On campaigns with a campaign-level budget they can also be a percentage of the campaign daily budget; the percentage auto-computes the absolute, like Ads Manager. ALWAYS `dry_run: true` first: show the returned plan (source ad set, the exact ads that will be recreated, per-copy overrides) and execute only after the user confirms. Partial failures come back per copy and per ad in `data.copies[].errors` with Meta's own error text: relay them as Meta wrote them.
- `adsap_update_entity`: budget, name and schedule edits on existing entities.
- `adsap_replace_ad_creative`: swap image, video or copy on a live ad (keeps the ad, replaces what it shows).
- `adsap_get_ad_review_status`: account-wide sweep of rejected, in-review and restricted ads with reasons. Use it for "why isn't this running?" before blaming budgets.
- Rejected ad under appeal? Meta locks it: deletion is impossible until the re-review completes, and an approved appeal clears the account-quality strike (better than deleting). Advise waiting it out, never "just delete it".
