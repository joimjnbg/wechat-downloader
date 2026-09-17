# 02b — Re-sync merges without duplicating rows

**What to build:** Re-running sync on the Sync page merges new Articles into the existing rows instead of duplicating or wiping them.

**Blocked by:** 02a — Sync rows with archived marks.

**Status:** ready-for-agent

- [ ] Re-sync merges new rows into existing list (no duplicates, no silent drops)
- [ ] Newly archived rows update their archived mark on re-sync
- [ ] Row order stays stable (publish-time desc, same as library)
