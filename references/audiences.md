# Audiences and targeting

*"Who am I targeting?", "Build a retargeting audience", "Find interests for X"*

- `adsap_list_custom_audiences`: inventory with sizes and types.
- `adsap_manage_custom_audience`: create, update or delete rule-based audiences (website visitors, engagement, lookalikes, app users). **Before any delete**: run `adsap_get_custom_audience_ads` and show the user which ads still use the audience.
- Detailed targeting covers interests, behaviors and demographics such as parents, job titles, education and life events. `adsap_search_detailed_targeting` searches every type at once, like the search box in Ads Manager, and returns each result's `type`. Put the results in `targeting.flexible_spec` groups of `adsap_create_ad_sets`: OR inside a group, AND between groups. Example: `[{interests: [Cooking], behaviors: [Frequent Travelers]}, {family_statuses: [Parents (All)]}]` (items are `{id, name}` from the search) reaches people who like cooking OR travel often, AND are parents. Before creating, tell the user the logic in those words, and size the audience with `adsap_get_delivery_estimate` using the same groups.
- Separate `interests` and `behaviors` lists are combined with AND by Meta, so never promise OR across them; use one group instead. Meta only supports this AND of ORs: "(A and B) or (C and D)" needs two ad sets. Meta removed "exclude people who match" for detailed targeting in 2025; exclude custom audiences instead.
- Housing, employment and financial campaigns accept approved interests only: pass `special_ad_category` to the search.
- More research: `adsap_search_interests`, `adsap_search_behaviors`, `adsap_search_geo_locations`.
