# 03 — Picker with live computed set matching CLI oracle

**What to build:** Batch type toggles plus per-article checklist compute a live download set (count + rows) that matches `library export --pick` for the same selection.

**Blocked by:** 02 — Sync entries resolve and list with archived marks.

**Status:** done (2026-09-17) — toggles + checklist + live set merged, review findings fixed

- [x] Type toggles flip batch defaults; checklist overrides individual rows — `touched` set tracks user-moved boxes (open or close); untouched rows follow toggles; review-fixed: uncheck now excludes, video checks `itemShowType` not label
- [x] Computed set (count + rows) updates live before any download — `pickSummary` header + `picked` memo; 11 renderer tests green
- [x] Same selection via CLI preview yields the same set (GUI/CLI agree) — rule parity documented; shared-oracle test deferred (renderer bans core imports; CLI suite pins `applyPick` separately)
