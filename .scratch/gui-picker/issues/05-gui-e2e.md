# 05 — GUI e2e over the Sync page

**What to build:** End-to-end coverage of the Sync page with fixture cover + article pages: toggles, checklist, shown set, and one download-through in an isolated library.

**Blocked by:** 04 — Download wiring with progress, cancel, retry.

**Status:** done (2026-09-17) — e2e block merged, static-reviewed, NOT run (no display on this box)

- [x] Toggles flip batch, checklist overrides, shown set matches before download — `waitForFunction` on count text (no sleeps); real account option picked from dropdown (not hardcoded name)
- [x] One download-through lands content.md + meta.json in isolated library — waits on new `library.json` id, then asserts `content.md` + `meta.json` per fresh dir (not `>=1` count)
- [x] Live network never in CI; e2e runs locally only — fixture server + isolated dirs reused; run `npm run test:e2e` on a Mac/Linux box before calling this verified
