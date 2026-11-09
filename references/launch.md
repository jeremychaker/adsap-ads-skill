# Launch something new on Meta

*"Create a campaign", "Launch these ads", "Set up retargeting", "Boost my Instagram post"*

## Contents

- The launch checklist
- Standard chain, five steps
- Shortcuts: Instagram boost, Instant Forms, templates, app promotion, ad sheets
- Budgets: units and placement
- Special Ad Categories
- Worked examples

## The launch checklist

Copy this checklist and check items off as you go:

```
Launch progress:
- [ ] 1. Creative ready (existing asset picked, or upload completed)
- [ ] 2. Campaign created (PAUSED)
- [ ] 3. Ad set(s) created, targeting built, reach sanity-checked
- [ ] 4. Ad created
- [ ] 5. Preview shown to the user
- [ ] 6. User explicitly said launch, then activate
```

## Standard chain, five steps

Each create step follows safety rule 2: `dry_run: true` first, always. On claude.ai the preview renders a launch review card and the user confirms in the card (`references/cards.md`); without cards, show the plan in text, get a confirmation, then execute.

1. **Creative in place.** Existing assets via `adsap_list_creative_assets`: it can filter by `name_contains`, `image_hash` or `video_id` (find the library entry for an asset seen in `adsap_get_ad`), `group_id` or `created_after`, and `skip_thumbnails: true` gives a lightweight listing for scanning big libraries. On claude.ai it renders a creative picker card (`references/cards.md`). Or upload new ones: browse `adsap_list_drive_files` (Google Drive) or `adsap_list_dropbox_files` (Dropbox), both connected in the web app, then `adsap_upload_creative` (pass exactly ONE of `drive_file_id` or `dropbox_path`). Images finish fast; videos process async, so poll `adsap_get_upload_status` (on claude.ai a live progress card tracks it). **Drive visibility:** Google Drive shows Adsap only the files the user picked in the web app on the Creative Upload page. An empty `adsap_list_drive_files` result means nothing was picked yet, not an empty Drive: ask the user to pick the files there (or use Dropbox or the web uploader). Never pass an id taken from a Drive link; it returns `DRIVE_FILE_NOT_VISIBLE`. **Paired uploads:** the user may upload square and vertical versions of one creative for multi-placement ads, as filenames sharing a base name with suffixes like `_feed` and `_story` or `_1x1` and `_9x16`. Pass `pair: true` on EACH file's upload and Adsap groups them automatically; upload order does not matter. Pairing is opt-in: set the flag only when the user wants the files linked, and remember BOTH uploads need it. Re-uploading a filename that already exists in the account will not re-pair it; use fresh filenames.
2. **`adsap_create_campaign`.** Needs an objective; ask the user's goal (sales, leads, traffic, awareness) if unclear.
3. **`adsap_create_ad_sets`.** Up to 20 per call; requires `campaign_objective`. Targeting at creation supports geo, age and gender, custom-audience include and exclude (lookalike ids go in `custom_audiences`), detailed targeting groups in `flexible_spec` (`references/audiences.md`), or a `saved_audience_id` (replaces manual targeting entirely). Sanity-check reach with `adsap_get_delivery_estimate` before creating, passing the same targeting, `flexible_spec` groups included; its summary states the group logic in words, so quote it back to the user. Leads campaigns take three destinations: `ON_AD` (Instant Forms), `WEBSITE`, and `WEBSITE_AND_LEAD_FORM` (Website and Instant Forms: `optimization_goal` OFFSITE_CONVERSIONS with `pixel_id` and `conversion_event`, usually LEAD; ads on it need BOTH `link_url` and `leadgen_form_id`). WhatsApp ad sets: `destination_type` WHATSAPP with `optimization_goal` CONVERSATIONS on traffic, engagement, leads or sales campaigns, and a `page_id` whose primary WhatsApp number is a WhatsApp Business account. Ads in them need no link. **Not supported by Adsap yet:** Messenger and Instagram Direct ad sets, calls, event responses, reminders and shop destinations. If the user asks for one, say it is not available in Adsap yet and point them to Meta Ads Manager for that ad set. Never quietly substitute a website or profile destination.
4. **`adsap_create_ad`.** Attach creative, copy and CTA. The creative is exactly ONE of five pointers in `creative_spec`. `feed_creative_name` names a library file (the square or 4:5 one); `vertical_creative_name` adds the 9:16 file of the pair, and a vertical-only ad is fine: pass `vertical_creative_name` alone and the vertical file becomes the ad's single asset, as when one 9:16 file is uploaded in Ads Manager. `image_hash` or `video_id` reuses an existing ad's image or video with NEW copy, ids from `adsap_get_ad`. If the asset is not in the library yet, `adsap_import_account_creatives` brings the account's whole existing image and video library in. `creative_id` reuses a WHOLE existing creative as-is, and `object_story_id` runs an existing Page post as the ad. The last two keep the original post and its likes and comments, and need no copy fields at all. `instagram_user_id` is optional: leave it out when the brand has no Instagram account and the Facebook Page represents it on Instagram (the Use Facebook Page option in Ads Manager). Never pass another business's Instagram account; Meta rejects it with "Ad account has no access to this Instagram account".
5. **Show the result** with `adsap_get_ad_preview`. Activation happens only on an explicit "launch" from the user: in the launch review card's own Activate step on claude.ai, or in chat where no card rendered.

## Shortcuts

- **Boost an IG post**: `adsap_list_ig_media` finds the post. `adsap_list_ad_sets` picks the destination ad set, whose budget and targeting the ad inherits; on claude.ai the user can tick the ad set in the manage card, see `references/cards.md`. `adsap_create_ad_from_ig_post` creates it, preview first (it defaults to `dry_run: true`).
- **Lead form (Instant Form)**: a lead ad needs a form that belongs to the SAME Facebook Page as the ad. `adsap_list_meta_assets` (asset_type `instant_forms`) lists the Page's forms and `adsap_get_instant_form` reads one in full. No suitable form? `adsap_manage_instant_form` with action `create`: ask for the questions. Two sections have saved defaults per Facebook Page in Adsap, on the Instant Forms page. They are the privacy policy link (Meta requires one) and the thank-you screen (headline, optional text, button or no button). Leave `privacy_policy` and `thank_you` out; the preview applies the saved defaults and lists them (`defaults_used`), so show them to the user. If the preview says they are missing, ask the user for them. Never invent any of the three; suggest wording only if the user asks. Meta NEVER lets anyone edit or delete a form once created, only archive it. So always preview first (`dry_run` true, free, creates nothing), read the summary back to the user, and create only after they confirm. To change a form: action `duplicate` with a new name plus only the sections to replace, then `archive` the old one. Limits to state plainly: at most 3 short answer questions per form (multiple choice is not capped). Conditional logic is not available (Meta keeps it for its own interface). Not built in Adsap yet: appointment, store locator and national ID questions, and file downloads on the thank-you screen. The new `form_id` goes into `creative_spec.leadgen_form_id` of `adsap_create_ad`, on an Instant Forms (or Website and Instant Forms) ad set.
- **Saved defaults**: `adsap_list_ad_templates` lists the user's templates; apply a template's settings to new creates when the user has them.
- **Promote a mobile app**: `adsap_list_apps` lists the apps the ad account can promote and supplies the `application_id` and `object_store_url` an app-promotion campaign needs. Apps in Development mode appear in the list but Meta refuses to promote them; the app must be Live.
- **Check a bulk Ad Sheet**: `adsap_list_ad_sheets` (Meta and Google sheets with status and row counts), then `adsap_get_ad_sheet_schema` (one sheet's columns). Read-only here: sheets are built, edited and launched in the web app.

## Budgets: units and placement

**Budgets on Meta are in CENTS** of the ad account's own currency, so `daily_budget: 5000` means 50.00. The Google create and update tools do the reverse: they take a plain number (`12.50`). Never convert between currencies and never debate which currency applies. Pass the user's figure as-is, in the account's currency from step 1 of the session start.

**Budget placement.** Adsap defaults to **ad-set budgets**: pass no campaign budget and each ad set carries its own (the preview confirms this as `budget_strategy: "ad_set"` with a warning saying so). Use campaign-level budget sharing (`campaign_budget_optimization: true`) only when the user asks for one pooled budget across ad sets. Do not deliberate the choice: default to ad-set budgets and mention the alternative in one clause if it is relevant.

## Special Ad Categories

Housing, employment and financial products and services (`FINANCIAL_PRODUCTS_SERVICES`; `CREDIT` is the retired name) lock targeting to ages 18 to 65 and all genders with a 15 mi / 25 km radius floor. Social issues, elections and politics, and online gambling and gaming (`ONLINE_GAMBLING_AND_GAMING`, which needs Meta's gambling authorization on the ad account) keep normal targeting. Every declared category confines ad set locations to the countries declared on the campaign (`special_ad_category_country`, one or several): warn the user before they are surprised.

## Worked examples

**Creation request (safety-critical)**
User: "Create a campaign for our summer sale, €50/day."
Flow: ask the goal if unclear (sales? traffic?), then `adsap_create_campaign` with `dry_run: true`. On claude.ai the launch review card takes over (`references/cards.md`). Without cards: show the plan, then on confirmation create for real. Then the same per step for ad sets, then the ad, then `adsap_get_ad_preview`.
Answer shape: "Everything is created **paused**, nothing is spending." Activation only on an explicit "launch" from the user. Do NOT activate from chat, even though the user asked to "create".

**Boost an Instagram post**
User: "Boost my latest IG post."
Flow: `adsap_list_ig_media` finds the post. `adsap_list_ad_sets` picks the ad set that carries it; ask which one, since the ad inherits that budget and targeting, or on claude.ai read the ticked choice from the manage card. `adsap_create_ad_from_ig_post` defaults to a preview: show the plan, confirm, create.
Answer shape: confirm the ad exists, is paused, and which ad set's budget it will use once activated.
