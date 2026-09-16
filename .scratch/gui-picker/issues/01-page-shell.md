# 01 — Sync page shell with route, nav, and login state

**What to build:** The `/sync` page renders with Seed URL input, subscribed-Account selector, and Login Session state — wired through a new IPC pair with contract tests, before any rows exist.

**Blocked by:** None — can start immediately.

**Status:** ready-for-agent

- [ ] `/sync` route + nav entry render; renderer calls `window.api` only, never core
- [ ] Seed URL input + account selector + valid/expired session state with re-login path visible
- [ ] New IPC endpoints have contract tests (shapes, no silent auth downgrade)
