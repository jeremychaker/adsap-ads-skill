# Cards on claude.ai

On claude.ai several Adsap tools render a SELF-CONTAINED interactive card instead of plain data. The rules below apply only where cards render. On an assistant without cards (ChatGPT, Perplexity), the same tools return data you present in text, and nothing here applies.

## The two rules most cards share

1. **After one of these cards renders, reply with ONE short sentence and stop:** manage, launch review, ad preview, creative picker, gallery and plan. Do not repeat the card's table, checklist, creatives or copy as text, and do not describe the card's tabs or buttons. The insights card is the exception, see its section.
2. **Read the card's widget context before answering anything about what the user did in it.** Every card records its current selection and every change the user made. Whenever the user refers to something they did in a card ("the one I ticked", "use this one", "what did I just change"), READ that widget context BEFORE answering. Never tell the user you cannot see their in-card actions without reading it first.

## Manage card (`adsap_list_campaigns`, `adsap_list_ad_sets`)

The user can TICK rows to select campaigns or ad sets, pause or activate them, and edit budgets inside the card. The card records the selection and every change in its widget context. A ticked ad set is a CHOICE: its id goes straight into `adsap_create_ad` or `adsap_create_ad_from_ig_post`. Read the widget context for it instead of asking the user to name the ad set again.

## Launch review card (`adsap_create_campaign`, `adsap_create_ad_sets`, `adsap_create_ad` with `dry_run: true`)

The preview of each of the three Meta create tools renders a launch review card where the USER confirms, creates (always paused) and can activate. After it renders, reply with ONE short sentence and STOP. Never run the real create from chat unless the user explicitly asks in a new message AND the card's activity log shows nothing was created there. Activation is the card's own Activate step; do not activate from chat because the user asked to "create".

## Insights card (`adsap_get_insights`)

ONE `adsap_get_insights` call per account and date range is enough. Its card always renders BOTH the daily account trend AND the ranked entity table. A second call at another level for the same account and range only produces a duplicate card. Under this card, write the analysis and the recommendations the performance reference asks for; the card already carries the numbers, so do not retype its table as text.

## Ad preview card (`adsap_get_ad_preview`)

The actual ad arrives as an interactive card. After it renders, reply with ONE short sentence and stop: never list or offer other placements and never describe the card's tabs or buttons. If the user asks about another placement, the card's "All placements on Meta" button covers them.

## Creative picker card (`adsap_list_creative_assets`)

A thumbnail grid with a type filter and multi-select. After it renders, reply with ONE short sentence and stop; never list the creatives as text. When the user says to use their selection, read the widget context for the current picks: each pick's name is the exact `feed_creative_name` the ad needs.

## Upload progress card (`adsap_get_upload_status`)

A live progress card tracks a video upload while it processes. Poll `adsap_get_upload_status` as the launch reference says; the card updates on its own.

## Gallery card (`adsap_get_ad_library_snapshot`)

One call with up to 8 ad ids renders ONE gallery card the user clicks through (video ads play, carousels show their slides). After it renders, reply with ONE short sentence and stop: do not describe the creatives or repeat the copy as text.

## Plan card (`adsap_manage_onboarding_plan`)

Every call renders a plan card with a progress ring, the next step, and check-offs the user saves inside the card. After it renders, reply with ONE short sentence and stop; do not repeat the checklist as text. The card logs the user's saves to the widget context: read it before updating step statuses from chat, so you never overwrite what they already saved.
