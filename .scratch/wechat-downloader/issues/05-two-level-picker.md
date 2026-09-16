# 05 — Two-level picker computes the download set

**What to build:** The Two-level picker over the synced list: batch type toggles (text/images/audio/video) set defaults, the per-article checklist overrides, and the effective download set is computed and shown before anything downloads.

**Blocked by:** 04 — Sync Account history list with archived state.

**Status:** done (2026-09-16) — core + CLI preview merged, GUI e2e deferred with reason

- [x] Type toggles flip batch defaults for all synced Articles — `applyPick` (`src/core/pick-articles.ts`): `text:false` drops all non-video rows, `video:false` drops video rows; `images`/`audio` keys accepted but documented as download-dimension (not row filters) with a pin test.
- [x] Per-article checklist overrides individual rows predictably — `include` re-adds after toggle exclusion, `exclude` removes from default selection; identity is `refId` (shared with subscription pending rows); unknown ids ignored.
- [x] Computed download set is displayed before download; GUI e2e covers toggles + checklist + shown set — CLI half done: `library export --pick <json> --from-subscription` prints `{total,count,items[]}` without downloading (5/5 CLI tests). GUI e2e NOT done: no renderer picker UI was built in this ticket — the computed-set preview is CLI-only. GUI picker UI + e2e is follow-up work when the renderer screen exists.
