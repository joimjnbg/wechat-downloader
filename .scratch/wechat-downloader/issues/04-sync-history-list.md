# 04 — Sync Account history list with archived state

**What to build:** Syncing an Account produces a stable identity-keyed Article list (title, publish time, type, archived or not) that the picker can consume — list-first, download-second.

**Blocked by:** 02 — History-depth spike against target Account; 03 — Seed URL resolves Account, QR Login Session persists.

**Status:** done (2026-09-16) — verified against upstream at CLI seam, pin test added, no fork product changes

Mapping (v1 staged scope: latest-only via cover — ticket 02):

- [x] Sync returns identity-keyed list (stable across re-syncs, not URL-form dependent) — new-detection keys on cover `sourceId` (reviewId); pending-row `refId` falls back to `~`/`_`-normalized URL key (`sourceUrlKey`), so URL-form variants map to one row. Pinned by `tests/cli/sync-list.test.ts` (3/3: first-run pending row, re-run `newFound: 0` with identical refIds, new-cover merges instead of overwriting).
- [x] Each entry shows title, publish time, type, already-archived state — with v1 cover limits stated honestly in the pin: title + real `sourceId` asserted; `itemShowType` asserted absent (cover carries no type); `createTime` is discovery-time placeholder (cover has no publish time); archived-state transitions ride the pre-existing `isRefDownloaded`/`removeNewRefs` + digest `downloaded` paths, not re-pinned here.
- [x] Re-sync marks newly archived entries without duplicating rows — merge-not-overwrite (`mergeNewRefs`) plus cursor-advance (`updateWatermark` + `latestArticleId`) pinned; second run yields `newFound: 0`, one subscription row, same refIds.
