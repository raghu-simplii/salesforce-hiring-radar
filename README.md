# Salesforce hiring radar

Salesforce hiring scan across a tracked company list, plus US staffing leads.

Live page: https://raghu-simplii.github.io/salesforce-hiring-radar/

## Status

**The scan does not run on a schedule.** There is no Action, cron or scraper in
this repo — every refresh is a session someone runs by hand. It last ran
2026-09-16 and sat stale for 57 days before anyone noticed, which is why the
page now shows a freshness banner when the data is more than a few days old.

## Refreshing

See [RUNBOOK.md](RUNBOOK.md) — sources, filters, where the data lives, and how
to verify a scan before publishing it.

Short version: data is embedded in `index.html` as JavaScript constants, not
loaded from `data.json` (which is stale and unused). LinkedIn and ZipRecruiter
refuse automated access, so a refresh needs a signed-in browser.

## Config

Edit `config.json` to change companies, add companies, change the keyword, or
change lookback windows (`windows_days`).
