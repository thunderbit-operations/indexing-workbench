# Thunderbit Indexing Workbench

Static page that lists all Thunderbit EN pages currently not indexed by Google, grouped into batches of 10 for daily manual GSC "Request Indexing" submission.

**Live:** https://thunderbit-operations.github.io/indexing-workbench/

## How it works

- Source data: daily GSC URL Inspection scan in `~/Library/Application Support/thunderbit-gsc-monitor/`
- Generator: `build_workbench.py` reads the latest scan JSON and rewrites `index.html`
- Automation: launchd rebuilds and pushes after every 08:00 scan
- Workflow: intern opens the page, picks the first unfinished group, copies all 10 URLs, pastes each into GSC and clicks "Request Indexing"

URLs that get indexed drop off the list automatically the next morning. Round 2 happens naturally — whatever remains after the first pass cycles back to the top.
