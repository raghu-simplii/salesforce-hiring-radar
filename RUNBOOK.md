# Refresh runbook

How the Salesforce hiring radar gets refreshed. Written 2026-11-12, after the
scan had been stopped for 57 days without anyone noticing.

## Why this file exists

Every one of the ~168 refresh commits was produced by hand in a chat session.
Nothing in this repo runs on a schedule — there is no GitHub Action, no cron, no
scraper. When the chat sessions stopped on 2026-09-16, the scan stopped too, and
the page kept serving Sep 16 data under a "scans every day" reputation until
November. The method lived only in those sessions. It lives here now.

## Where the data actually is

All page data is **embedded in `index.html`** as JavaScript constants. There is
no build step and no runtime fetch.

| Constant | Line (approx) | Feeds | Count at last refresh |
|---|---|---|---|
| `DATA` | ~1089 | Overview, Companies, Hit board | 578 hits |
| `STAFFING_LEADS` | ~14279 | US Staffing Leads tab | 36 leads |
| `STAFFING_CONTACTS` | ~14785 | Staffing Leads Contacts tab | 114 contacts |

`config.json` holds the 17 tracked companies and the scan window. It is read by
a refresh run, **not** by the page.

`data.json` is stale and unused — it last moved on 2026-08-18 while the page
moved on past it. Do not trust it; do not wire anything to it.

### Key fields

`DATA.hits[]` — `author_company`, `post_date`, `post_url`, `post_text_excerpt`,
`roles[]`, `location`, `source_type`, `company_key`, `is_salesforce`, `still_open`.

`STAFFING_LEADS[]` — `title`, `company`, `location`, `source`, `url`,
`posted_date`, `seniority_guess`, `size_band`, `why_included`.

`DATA.end_date` drives the freshness banner (below). Always set it to the real
scan date.

## Sources, and what each one costs to reach

This is the part that decides whether a refresh is even possible from where you
are sitting.

| Source | From a cloud session | From a logged-in browser |
|---|---|---|
| LinkedIn | **Blocked.** 403 at the network layer; `robots_disallowed` to any fetch tool | Works |
| ZipRecruiter | **Blocked.** `robots_disallowed` | Works |
| Indeed | Works | Works |
| Company careers pages | Mixed — many are JavaScript-rendered and return a shell with no listings | Works |

LinkedIn supplies most of Tab 1. **A refresh run with no browser cannot rebuild
this page.** It can top up Indeed-sourced staffing leads and little else. If you
find yourself without browser access, say so and stop — do not publish a partial
scan under a fresh date, because that hides the gap instead of showing it.

The working path is a browser already signed in to LinkedIn, driven through the
Claude desktop app on Raghu's laptop. That is the "built-in hack" — not a
bypass, just a real session doing what a person's session may do.

## Running a refresh

1. Clone the repo. Read `config.json` for the company list and window.
2. For each of the 17 companies: LinkedIn company jobs + the careers page named
   in config. Apply that company's `keyword`, `role_include_examples` and
   `role_exclude`. `it_only` is true globally — IT / software / Salesforce /
   data / cloud / QA only, no business roles.
3. Re-verify existing hits. A posting is dropped only when the JD is **gone**.
   Treat HTTP 429 / authwall / rate limit as *blocked*, never as closed — the
   existing `why_included` notes follow this rule and it matters, otherwise a
   bad afternoon of rate limiting silently deletes live roles.
4. For Tab 2: US mid-market, junior/admin Salesforce titles, direct employers
   only (filter out agencies), prefer under ~2k employees and cap at ~5k.
   Deliberately excluded: senior and specialized titles, IT services firms,
   large enterprises.
5. Rewrite the three constants in `index.html`. Set `DATA.scanned_at`,
   `scanned_at_display`, `end_date`, `end_date_display` and the window.
6. Verify before publishing — see below.
7. Commit in the established style:
   `YYYY-MM-DD hiring radar refresh: N IT hits (+/-D), staffing M`

## Verify before publishing

Open the rendered page and confirm: both tabs populate, the posted-date chips
(1/3/7/14/30 days) filter correctly, counts match the commit message, and the
freshness banner has cleared. Spot-check several listings against their live
URLs.

**Never invent a posting.** Every row must trace to a real listing someone can
open. The page's own empty states say "No posts were invented" — honour that. A
thin honest scan beats a padded one, and a padded one is worse than no refresh
at all because it cannot be told apart from a good one later.

## The freshness banner

`renderStaleBanner()` in `index.html` compares `DATA.end_date` to today:

- 0–3 days — hidden
- 4–13 days — amber, "Data is ageing"
- 14+ days — red, "Do not work this list as current"

This exists so a stopped scan is visible on the page itself rather than
depending on someone noticing a date. Do not remove it, and do not set
`end_date` to anything other than the real scan date to quiet it.

## Known gaps

- No automation. A scheduled run needs the laptop online with the desktop app
  running, and needs push rights to this repo.
- Push is not available from an arbitrary cloud session — the repo has to be in
  that session's authorized set.
- `~/work/refresh-2026-10-07-pm` on the laptop suggests an Oct 7 refresh was
  started and never committed. Worth a look before the next run.
- `data.json` should be deleted or regenerated; right now it is a trap.
