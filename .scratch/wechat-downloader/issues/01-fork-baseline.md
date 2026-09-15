# 01 — Fork wx-kit and run baseline

**What to build:** Fork wx-kit into this project so the app boots and downloads one fixture Article end-to-end (GUI opens, CLI JSON works, library on disk receives the Whole-article archive).

**Blocked by:** None — can start immediately.

**Status:** in-progress — baseline cloned to `wx-kit-upstream/` at v0.11.1; investigating Windows verification path

Evidence so far (2026-09-15):

- `wx-kit-upstream/` cloned from monkeychen/wx-kit (upstream HEAD `ff14157`, v0.11.1); `npm install` OK; `npm run typecheck` clean.
- `npm test`: 669 passed / 7 failed. Failures are Windows-environment-only, not regressions: `protocol-resolve` (3, expects POSIX `/lib/root/...`, gets `D:\...`), `cli-link` (3, POSIX symlink/PATH assumptions), `weread creds-store 0600` (1, Windows chmod semantics). Core download/library suites pass: `download-article` 16/16, `download-queue` 15/15, `cli-contract` 48/48.
- Electron CLI (`npx electron . version`) hangs on this Windows box (no display / GUI-subsystem stdout issue noted upstream) — verification pivots to `runCli` in-process + vitest suites, not the packaged binary.
- Suggested follow-up for ticket 02 owner: pin upstream commit `ff14157` and record which of the 7 Windows-only failures are accepted vs fixed; do not chase POSIX-only paths on Windows.

- [ ] App boots from fork (GUI opens, CLI responds with JSON + exit codes)
- [ ] One fixture Article downloads to isolated library with text + media + metadata
- [ ] Existing unit suite passes on the fork
