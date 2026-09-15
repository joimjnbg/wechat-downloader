# 06 — Download set to Whole-article archive with dedupe and honest failures

**What to build:** Downloading the picker's set produces one Whole-article archive per Article (text + selected media localized + metadata with warnings), skips already-archived Articles, records per-Article failure reasons honestly, and stays drivable via JSON CLI.

**Blocked by:** 05 — Two-level picker computes the download set.

**Status:** ready-for-agent

- [ ] Each downloaded Article has text, selected media saved locally, metadata + warnings
- [ ] Already-archived Articles are skipped and reported as skipped (no duplicates)
- [ ] Reader-inaccessible vs transient failures are distinct; nothing reports silent success; CLI flags expose the picker set
