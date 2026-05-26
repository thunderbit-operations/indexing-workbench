# Thunderbit Indexing Workbench

Single-page static workbench listing Thunderbit pages currently not indexed by Google, grouped into batches of 10 for daily manual GSC "Request Indexing" submission. Covers EN + 10 localized sites via a top tab switcher.

**Live:** https://thunderbit-operations.github.io/indexing-workbench/

## Locales

| Tab | Source | Refresh |
|---|---|---|
| EN | Daily GSC URL Inspection scan | Auto every 08:00 |
| KO · JA · AR · PT · 繁中 · DE · ES · FR · IT · NL | GSC Coverage Drilldown export + HEAD-200 filter | Manual rerun |

Each URL list is filtered to remove HTTP 3xx/4xx/5xx pages and anything blocked by the live `robots.txt` (e.g. `/blog/all-articles/*` pagination).

## How it works

1. **EN** — `check_en_indexing.py` runs every 08:00 via launchd, inspecting ~250 not-indexed URLs against the GSC URL Inspection API.
2. **Other locales** — `filter_locales/head_check.py` HEAD-checks the URLs exported from each locale's GSC Coverage Drilldown report (10 concurrent, 0.2s sleep, safe under main-site throttle red line).
3. `build_workbench.py` merges all sources, applies `robots.txt` disallow filtering, sorts by oldest last-crawl date, batches into groups of 10, and writes `index.html`.
4. `run_daily.sh` runs build → `git push` after the EN scan; GitHub Pages auto-rebuilds.

URLs that get indexed drop off the EN list automatically the next morning. Other locales drop only when `head_check.py` is rerun against a fresh GSC export.

## Daily intern workflow

1. Open the live page.
2. Pick the active locale tab.
3. Click **Copy all 10 URLs** on the top unfinished group.
4. Click **Open GSC ↗** on any row → in Search Console, paste each URL into the top inspection search bar → click **Request Indexing**.
5. Repeat the next day; previously-completed URLs drop off (EN) or stay until the next manual rerun (other locales).
