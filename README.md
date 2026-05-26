# Thunderbit Indexing Workbench

Single-page static workbench listing Thunderbit pages currently not indexed by Google, grouped into batches of 10 for daily manual GSC "Request Indexing" submission. Covers EN + 10 localized sites via a top tab switcher.

**Live:** https://thunderbit-operations.github.io/indexing-workbench/

## Locales

| Tab | Source | Refresh |
|---|---|---|
| EN | Daily GSC URL Inspection scan | Auto every 08:00 |
| KO · JA · AR · PT · 繁中 · DE · ES · FR · IT · NL | GSC Coverage Drilldown export + HEAD-200 filter | Manual rerun |

Each URL list is filtered to remove HTTP 3xx/4xx/5xx pages and anything blocked by the live `robots.txt` (e.g. `/blog/all-articles/*` pagination).

## Daily intern workflow

1. Open the live page.
2. Pick the active locale tab (round-1 priority: EN first, then KO → JA → AR → PT → 繁中 → DE → ES → FR → IT → NL).
3. Click **Copy** on any row, or **Copy all 10 URLs** on a group, to copy the URL(s) to your clipboard.
4. In Google Search Console (top-right of the toolbar), paste each URL into the inspection search bar and click **Request Indexing**.
5. Copied URLs are immediately marked **✓ checked** (greyed out, struck-through) for the rest of the day.
6. Tomorrow, every URL you checked today disappears from the workbench automatically — you keep moving down the queue without ever resubmitting the same page.

State lives entirely in your browser's `localStorage`. If you switch browser or clear data, you'll see previously-checked URLs reappear; just skip past them.

URLs the system later detects as indexed (EN: nightly GSC scan; other locales: when their HEAD filter is rerun) also drop off naturally.

## How it works under the hood

1. **EN** — `check_en_indexing.py` runs every 08:00 via launchd, inspecting all not-indexed EN URLs against the GSC URL Inspection API.
2. **Other locales** — `filter_locales/head_check.py` HEAD-checks the URLs exported from each locale's GSC Coverage Drilldown report (10 concurrent, 0.2s sleep).
3. `build_workbench.py` merges all sources, applies `robots.txt` disallow filtering, sorts by oldest last-crawl date, and writes `index.html`.
4. `run_daily.sh` runs build → `git push` after the EN scan; GitHub Pages auto-rebuilds.
5. The existing Lark webhook continues to notify daily scan results (recoveries + remaining not-indexed counts).
