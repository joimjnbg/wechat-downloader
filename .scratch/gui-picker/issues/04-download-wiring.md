# 04 — Download wiring with progress, cancel, retry

**What to build:** The computed set downloads through the existing queue with per-Article progress, clean cancel, and individual retry that skips successes.

**Blocked by:** 03 — Picker with live computed set matching CLI oracle.

**Status:** ready-for-agent

- [ ] Per-Article progress and results visible; cancel stops cleanly, keeps successes
- [ ] Failed Articles retryable individually; successes report skipped on re-run
- [ ] Unavailable vs transient failures stay distinct in row results
