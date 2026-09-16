# 07 — Library browse/search plus batch progress, cancel, retry

**What to build:** The local library is usable after downloading: browse, keyword search, filter by Account, read, delete, reveal in folder — with visible batch progress, cancel, and retry of failed Articles.

**Blocked by:** 06 — Download set to Whole-article archive with dedupe and honest failures.

**Status:** done (2026-09-16) — verified against upstream at CLI seam, pin test added, no fork product changes

- [x] Library supports browse, keyword search, Account filter, read, delete, reveal-in-folder — `list` / `search <kw>` / `list --account` / `remove --ids` (index + directory removal both asserted) pinned by `tests/cli/library-batch.test.ts`. Read = `content.md` on disk asserted in ticket 06; GUI reader + reveal-in-folder are renderer-only, covered by upstream e2e, not re-pinned here.
- [x] Batch shows per-Article progress and results; cancel stops cleanly — per-article stderr progress (`2/2`) + JSON summary pinned; cancel rides the pre-existing `shouldContinue`/AbortSignal path in `crawlAccount`/`DownloadQueue` (not re-pinned; no new cancel code in this scope).
- [x] Failed Articles are retryable individually without re-downloading successes — transient failure (status 500) retried by URL → success; already-archived re-download returns `skipped: 1`; unavailable stays honestly failed (retry-useless, kept distinct).
