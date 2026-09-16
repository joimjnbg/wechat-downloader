# 02 — Sync entries resolve and list with archived marks

**What to build:** Running sync from a Seed URL or a subscribed Account yields pending Article rows on the page, each with title, type, and already-archived mark.

**Blocked by:** 01 — Sync page shell with route, nav, and login state.

**Status:** ready-for-agent

- [ ] Seed URL resolves Account inline for confirmation; account selector syncs subscribed rows
- [ ] Rows show title, type, archived-or-not; identity keys stable across re-syncs
- [ ] Re-sync merges without duplicating rows
