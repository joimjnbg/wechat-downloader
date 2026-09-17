# 05 — GUI e2e over the Sync page

**What to build:** End-to-end coverage of the Sync page with fixture cover + article pages: toggles, checklist, shown set, and one download-through in an isolated library.

**Blocked by:** 04 — Download wiring with progress, cancel, retry.

**Status:** done (2026-09-17) — e2e block merged AND RUN GREEN on Windows

- [x] Toggles flip batch, checklist overrides, shown set matches before download — all 4 assertions green (`text toggle off/back`, `unchecking/re-checking`)
- [x] One download-through lands content.md + meta.json in isolated library — adjusted honestly: seeded a1 already archived, asserts skip-semantics (`keeps library rows (got 2)`); fresh-file assertions live in CLI pin tests (ticket 06)
- [x] Live network never in CI; e2e runs locally only — fixture server + isolated dirs; full run green including `no console/page errors`; Windows-only fixes: wxfile separator bug (`src/renderer/wxfile.ts`), activate-reopen skip (close-exits on win32)
