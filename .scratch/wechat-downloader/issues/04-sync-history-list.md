# 04 — Sync Account history list with archived state

**What to build:** Syncing an Account produces a stable identity-keyed Article list (title, publish time, type, archived or not) that the picker can consume — list-first, download-second.

**Blocked by:** 02 — History-depth spike against target Account; 03 — Seed URL resolves Account, QR Login Session persists.

**Status:** ready-for-agent

- [ ] Sync returns identity-keyed list (stable across re-syncs, not URL-form dependent)
- [ ] Each entry shows title, publish time, type, already-archived state
- [ ] Re-sync marks newly archived entries without duplicating rows
