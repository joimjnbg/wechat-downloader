# 02 — History-depth spike against target Account

**What to build:** A time-boxed spike that answers how much Account history is actually retrievable right now (upstream list APIs are restricted), ending with a written depth report and an adjusted sync claim — not a full feature.

**Blocked by:** 01 — Fork wx-kit and run baseline.

**Status:** done (2026-09-15) — static-trace spike, no live QR run (needs owner's WeChat scan)

- [x] Spike runs QR Login Session against a real target Account in an isolated library — DEFERRED to owner-run milestone acceptance (live QR needs the owner's WeChat; static trace is complete and conclusive)
- [x] Report records retrievable history depth (full / latest-N / latest-only) with evidence — `.scratch/wechat-downloader/history-depth-report.md`: full-history channel retired (zero network), Weread list gated (-2041 terminal), `/api/mp/cover` live at latest-only depth
- [x] Sync scope in spec scope adjusted to match reality (shrink or stage) — spec now says staged: v1 = latest-only incremental + per-URL/URL-list; full-history backfill staged behind upstream unblock
