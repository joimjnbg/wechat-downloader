# 02b — Re-sync merges without duplicating rows

**What to build:** Re-running sync on the Sync page merges new Articles into the existing rows instead of duplicating or wiping them.

**Blocked by:** 02a — Sync rows with archived marks.

**Status:** done (2026-09-17) — union merge merged, review findings fixed

- [x] Re-sync merges new rows into existing list (no duplicates, no silent drops) — `mergeSyncRows` union: same refId replaced (fresh flags), old-only rows kept, fresh appended
- [x] Newly archived rows update their archived mark on re-sync — replacement carries fresh `archived`; review-fixed (was reusing stale prev objects)
- [x] Row order stays stable (publish-time desc, same as library) — `createTime` plumbs through rows; build + merge both sort desc; review-fixed (was append-at-end)
