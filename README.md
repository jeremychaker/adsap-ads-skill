# Adsap skill for Claude, ChatGPT and Perplexity

The Adsap skill teaches an AI assistant how to run Meta and Google Ads through the [Adsap](https://adsap.ai) connector: which tool fits each job, in what order, and the safety rules that always apply. It follows the open [Agent Skills](https://agentskills.io) format.

A `SKILL.md` hub plus ten reference files in `references/`, one per job (performance, launch, optimize, audiences, tracking, catalog, research, Google Ads, troubleshooting, cards), zipped for upload. The assistant reads the hub first and opens only the reference file the job needs. The files contain no code, no keys and no credentials. Access to your ad accounts comes from the Adsap connector, which you add separately.

| | |
|---|---|
| Version | 3.3, updated 10 November 2026 |
| Connector address | `https://mcp.adsap.ai/mcp` |
| Setup guide | [adsap.ai/docs/guides/ai-copilot/setup](https://adsap.ai/docs/guides/ai-copilot/setup) |
| Skill page | [adsap.ai/skills/adsap-ads](https://adsap.ai/skills/adsap-ads) |

## Files

| File | For | Layout |
|---|---|---|
| `adsap-ads.zip` | Claude | `adsap-ads/SKILL.md` and `adsap-ads/references/` |
| `adsap-ads-chatgpt.zip` | ChatGPT | `plugin.json` plus `skills/adsap-ads/SKILL.md` and `skills/adsap-ads/references/` |
| `adsap-ads-perplexity.zip` | Perplexity | one `SKILL.md` at the root of the zip, the references appended as sections |
| `SKILL.md` and `references/` | reading | the same files, unzipped |
| `skill.json` | checking | version, date, size and SHA-256 of each file |

Three zips exist because each assistant expects its own layout: Claude takes the skill folder, ChatGPT takes it packed as a plugin, and Perplexity Computer takes a single `SKILL.md` at the root of the zip. The text inside is the same.

## Install

Connect the Adsap connector first, then install the skill. The full setup is in the [AI Copilot setup guide](https://adsap.ai/docs/guides/ai-copilot/setup).

| Assistant | Plans with skills | File | How to install |
|---|---|---|---|
| Claude | All plans, including Free | `adsap-ads.zip` | Customize, then Skills, then upload. Enable code execution under Settings, Capabilities first. |
| ChatGPT | Business, Enterprise and Edu | `adsap-ads-chatgpt.zip` | A workspace owner or admin opens Admin console, then Plugins, Add, Upload plugin. Members can upload too when the admin allows plugin uploads. To install a newer version later, open the plugin and choose Upload new version. |
| Perplexity | Pro, Max and Enterprise, inside Perplexity Computer | `adsap-ads-perplexity.zip` | Computer, then Skills, Create skill, Upload a skill. |

If you already have a skill named `adsap-meta-ads`, remove it first: the outdated predecessor of this skill.

Skills do not update themselves. When the version here changes, download the file again and upload it again. This repository always holds the files served on [adsap.ai/skills/adsap-ads](https://adsap.ai/skills/adsap-ads); `skill.json` carries their checksums.

## What the skill does

- **Session start.** Resolve the ad account first and read its currency and timezone from that response. Get Page, Instagram and pixel IDs from the asset list. Use only Adsap's tools, and reuse what is already in the conversation instead of fetching it again.
- **Safety rules.** Everything the assistant creates stays paused until you say launch. It previews every write before it runs it. It paces itself against the server's limits, states the date window on every performance answer, and never invents a number or a landing page URL.
- **Jobs.** Performance reads, launches on Meta and Google Ads, edits, audiences, pixels, catalogs, Instant Forms, templates, alerts and recommendations, each mapped to the tool that does it.

The skill is optional. The connector works without it, but the skill makes the copilot more reliable on multi-step work.

## About Adsap

Adsap is a Meta and Google Ads automation platform, with a web app and an AI copilot for Claude, ChatGPT and Perplexity. Launch ads in bulk, keep your settings on every launch, and approve every change before it runs. A free plan is available at [adsap.ai](https://adsap.ai).

Copyright To Better Digital SASU. The skill files are free to download, install and use with Adsap.
