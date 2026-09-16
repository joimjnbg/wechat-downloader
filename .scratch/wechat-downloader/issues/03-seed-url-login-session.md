# 03 — Seed URL resolves Account, QR Login Session persists

**What to build:** Pasting a Seed URL shows the resolved Account identity for confirmation, and scanning a QR establishes a Login Session that survives restarts with an honest valid/expired state (expiry never disguised as "no new articles").

**Blocked by:** 01 — Fork wx-kit and run baseline.

**Status:** done (2026-09-16) — verified against upstream, no fork code changes needed

Mapping (upstream already implements all three criteria at the CLI seam):

- [x] Paste Seed URL → Account name/id shown for confirmation before sync — `search --url <seed>` fetches the public article page, extracts `biz` via `extractArticleKeys`, normalizes to `MP_WXS_<digits>` bookId, reads `#js_name` via `parseAccount`, and (when logged in) cross-checks Weread `/book/info`. Pinned by new `tests/cli/search-account.test.ts` (3/3: resolve, NOT_FOUND exit 1, non-mp usage exit 2 zero-network).
- [x] QR scan → session persists across restart, GUI shows valid/expired — `login` QR flow (`qr-flow.ts`, 70s long-poll) writes 0600 `weread-creds.json`; `auth-status` reports present/valid without network, `--verify` probes renewal; GUI `LoginGate`/`Subscriptions`/`Settings` surface valid/expired with re-login path.
- [x] Expired session reports auth error with re-login path (CLI exit code = login error) — 401/403 and -2041/-2012/-2010 all throw `MpAuthExpired` (never downgraded to empty); digest/check-now surface `AUTH_REQUIRED` failures, exit 1, single attempt then stop. Pinned by `subscription-digest.test.ts` (AUTH_REQUIRED cases) + `weread-transport`/`parse-articles` auth tests.
