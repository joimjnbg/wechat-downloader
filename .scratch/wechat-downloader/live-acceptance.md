# Live acceptance checklist (needs owner)

Run in `wx-kit-upstream/` with an isolated library. Never CI. Do this before GUI picker work — results may reshape it.

## 0. Prereqs

- Clean typecheck + tests (done: typecheck clean, 692 passed / 7 pre-existing Windows-only failures).
- Isolated dirs, e.g. `$env:WXK_LIB = "D:\accept-lib"`, `$env:WXK_USER = "D:\accept-user"`.
- Note: on this Windows box `npx electron . <cmd>` hung at 180s (no display / GUI-subsystem stdout). If CLI hangs, redirect: `cmd > out.json 2> progress.log`, or run acceptance on mac/Linux.

## 1. Seed URL resolves Account (no login needed)

```
npx electron . search --url "<any article URL from target Account>"
```

- [ ] exit 0, JSON `account.fakeid = MP_WXS_<digits>`, `nickname` = Account name.

## 2. QR login persists

```
npx electron . login
npx electron . auth-status
# restart shell, then:
npx electron . auth-status
```

- [ ] QR prints, WeChat scan succeeds, `auth-status` shows present:true.
- [ ] After restart still present (no re-scan).

## 3. Sync = latest-only (ticket-02 claim)

```
npx electron . subscription check-now --out $env:WXK_LIB
npx electron . subscription check-now --out $env:WXK_LIB   # re-run
```

- [ ] First run: `newFound` ≤ 1 per Account. Second run: `newFound: 0`.
- [ ] If an Account yields 0 or errors: record code (`AUTH_REQUIRED` → re-login; `-2041` → gated as expected).

## 4. Download full archive

```
npx electron . download --url "<known article URL>" --formats md,meta --out $env:WXK_LIB
npx electron . library export --pick "{}" --from-subscription --out $env:WXK_LIB
```

- [ ] `content.md` + `meta.json` on disk, title correct.
- [ ] Re-download same URL → `skipped: 1`, no duplicate.
- [ ] Pick preview prints `{total,count,items[]}` without downloading.

## 5. Record outcome

Append results (pass/fail + codes + depth observed) to `.scratch/wechat-downloader/history-depth-report.md` and update ticket statuses. If depth differs from latest-only, ticket 04 scope reopens.
