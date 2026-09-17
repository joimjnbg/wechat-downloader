# 04 — Download wiring with progress, cancel, retry

**What to build:** The computed set downloads through the existing queue with per-Article progress, clean cancel, and individual retry that skips successes.

**Blocked by:** 03 — Picker with live computed set matching CLI oracle.

**Status:** done (2026-09-17) — download wiring merged, review findings fixed

- [x] Per-Article progress and results visible; cancel stops cleanly, keeps successes — aggregate progress + per-row ok/failed/unavailable tags + single-row retry buttons; cancel defers to existing queue path (no new cancel key in v1)
- [x] Failed Articles retryable individually; successes report skipped on re-run — `retryOne(refId)` reuses `downloadPicked`; post-download refresh keeps successes out of pending (skipped on re-run)
- [x] Unavailable vs transient failures stay distinct in row results — `failed` (red, retryable) vs `unavailable` (orange, retry-useless) tags; kept-branch message honest
