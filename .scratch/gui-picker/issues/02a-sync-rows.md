# 02a — Sync rows with archived marks

**What to build:** Running sync from a Seed URL or a subscribed Account yields pending Article rows on the Sync page, each with title, type, and already-archived mark — resolved inline, listed via existing IPC, no new IPC.

**Blocked by:** 01 — Sync page shell with route, nav, and login state (done).

**Status:** done (2026-09-17) — rows merged, review findings fixed

- [x] Seed URL resolves Account inline via `mp:search` for confirmation; account selector syncs subscribed rows via `subscriptions:checkNow` + `subscriptions:list` — resolve confirms inline (no auto-subscribe: confirm-then-sync keeps ticket scope); both entries run the check pipeline
- [x] Rows show title, type, archived-or-not (library cross-check); identity keys stable across re-syncs — `refId` (`mid_idx`, fallback normalized URL) plumbs through rows and list keys; archived checks canonical id first, URL second (short/long forms match)
- [x] Auth expiry surfaces as re-login path, never as empty rows — expiry sets session warning with Settings re-scan path; review-fixed: renderer drops direct core import (type via `window.api` shape)
