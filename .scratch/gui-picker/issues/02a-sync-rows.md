# 02a — Sync rows with archived marks

**What to build:** Running sync from a Seed URL or a subscribed Account yields pending Article rows on the Sync page, each with title, type, and already-archived mark — resolved inline, listed via existing IPC, no new IPC.

**Blocked by:** 01 — Sync page shell with route, nav, and login state (done).

**Status:** ready-for-agent

- [ ] Seed URL resolves Account inline via `mp:search` for confirmation; account selector syncs subscribed rows via `subscriptions:checkNow` + `subscriptions:list`
- [ ] Rows show title, type, archived-or-not (library cross-check); identity keys stable across re-syncs
- [ ] Auth expiry surfaces as re-login path, never as empty rows
