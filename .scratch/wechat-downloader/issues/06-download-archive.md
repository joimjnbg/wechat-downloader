# 06 — Download set to Whole-article archive with dedupe and honest failures

**What to build:** Downloading the picker's set produces one Whole-article archive per Article (text + selected media localized + metadata with warnings), skips already-archived Articles, records per-Article failure reasons honestly, and stays drivable via JSON CLI.

**Blocked by:** 05 — Two-level picker computes the download set.

**Status:** done (2026-09-16) — verified against upstream at CLI seam, pin test added, no fork product changes

- [x] Each downloaded Article has text, selected media saved locally, metadata + warnings — `download --url` writes `content.md` + `meta.json` (title/meta/warnings asserted on disk); images/videos ride the existing exporter paths (16/16 `download-article` + export suites green).
- [x] Already-archived Articles are skipped and reported as skipped (no duplicates) — second run returns `succeeded: 0, skipped: 1`, library stays at one row; canonical `mid_idx` identity (plus `~`/`_` retry + script-var backfill) pinned by `tests/cli/download-archive.test.ts`.
- [x] Reader-inaccessible vs transient failures are distinct; nothing reports silent success; CLI flags expose the picker set — error page yields `unavailable: 1` item (not generic failure); single-download summary carries `unavailable` (`realFailures` is crawl-layer derived); picker set flows via `library export --pick` (ticket 05) + `download --urls-file` batch.
