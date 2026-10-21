# Troubleshooting and error codes

Error responses include a `message` and often next steps: read them before improvising. Each entry below names the signal, then what to do.

- **Tools missing, or all calls failing.** The Adsap connector is disconnected: reconnect it in the assistant's connector settings, then retry.
- **A skill named `adsap-meta-ads` is also installed.** That is this skill's outdated predecessor: tell the user to remove it from their assistant's skills page so the two do not conflict.
- **This skill names a tool the connector says does not exist.** The connector is serving a cached tool list from before an Adsap update: disconnect and reconnect the Adsap connector, then start a fresh chat.
- **AUTH_EXPIRED, CALLER_META_TOKEN_REQUIRED, or "no active Meta token".** Their Meta connection lapsed: reconnect Meta inside the Adsap dashboard (app.adsap.ai, Settings).
- **CALLER_GOOGLE_TOKEN_REQUIRED.** The user has not connected Google Ads, or the connection was revoked: connect Google Ads inside the Adsap dashboard (app.adsap.ai, Settings).
- **RATE_LIMITED.** Stop and wait the cooldown given in the error; do not burn retries.
- **PLAN_RESTRICTED.** The workspace is on the Free plan and that tool or feature is Early Access: say which one, give `error.details.upgrade_url` (the Billing tab in the Adsap dashboard), never route around it.
- **QUOTA_EXCEEDED.** The Free plan's 10 tasks for the month are used: say so, give `error.details.upgrade_url` and `resets_at`; reads and analysis still work.
- **SESSION_LIMIT.** The session's pacing cap is hit: summarize what is done, stop calling tools, resume after about 30 minutes of inactivity (or hand bulk work to the web app).
- **DRY_RUN_REQUIRED.** An ACTIVE create was attempted without a preview: re-run the same call with `dry_run: true`, show the plan, then execute.
- **DUPLICATE_REQUEST.** An identical write landed in the last 30 seconds: if the repeat is intentional, wait half a minute and try once more.
- **UTILIZATION_HIGH.** Meta is throttling that ad account's API budget: wait a few minutes before more calls against it.
- **PERMISSION_DENIED mentioning write scope.** The API key behind the connector is read-only: create a new key with write access in the Adsap dashboard and reconnect.
- **ACCOUNT_NOT_SET_UP.** The account exists but its setup in the Adsap web app is unfinished. Relay the message as is (Settings in Adsap, Accounts Requiring Setup, Start setup) and stop. Only the workspace owner can finish setup, and no other tool will accept that account until then.
- **NOT_FOUND on something the user swears exists.** Usually the wrong ad account is selected: re-run `adsap_list_ad_accounts` and confirm.
