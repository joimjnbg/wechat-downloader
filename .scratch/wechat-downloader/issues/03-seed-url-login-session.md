# 03 — Seed URL resolves Account, QR Login Session persists

**What to build:** Pasting a Seed URL shows the resolved Account identity for confirmation, and scanning a QR establishes a Login Session that survives restarts with an honest valid/expired state (expiry never disguised as "no new articles").

**Blocked by:** 01 — Fork wx-kit and run baseline.

**Status:** ready-for-agent

- [ ] Paste Seed URL → Account name/id shown for confirmation before sync
- [ ] QR scan → session persists across restart, GUI shows valid/expired
- [ ] Expired session reports auth error with re-login path (CLI exit code = login error)
