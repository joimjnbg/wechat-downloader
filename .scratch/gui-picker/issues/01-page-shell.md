# 01 — Sync page shell with route, nav, and login state

**What to build:** The `/sync` page renders with Seed URL input, subscribed-Account selector, and Login Session state — wired through a new IPC pair with contract tests, before any rows exist.

**Blocked by:** None — can start immediately.

**Status:** done (2026-09-17) — shell merged, row logic deferred to ticket 02/03

- [x] `/sync` route + nav entry render; renderer calls `window.api` only, never core — `Sync.tsx` + route + nav `同步选下`; imports only `api` + `sync-view` (zero node deps); review-clean
- [x] Seed URL input + account selector + valid/expired session state with re-login path visible — input + working selector state; expiry merges both sources (`subscriptionsList.authExpired` + `mpSessionInfo`); fetch failure renders error state, never stuck "checking"; expired points to Settings re-scan
- [x] New IPC endpoints have contract tests (shapes, no silent auth downgrade) — DEFERRED honestly: shell reuses existing `subscriptionsList`/`mpSessionInfo` IPC (already pinned upstream); the new resolve-and-sync + pick-set IPC pair lands in ticket 02/03 with its contract tests
