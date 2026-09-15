# History-depth spike report (ticket 02)

Date: 2026-09-15. Upstream: `wx-kit-upstream/` at `ff14157` (v0.11.1). Method: static code + docs trace. No live QR run — that needs the owner's WeChat scan against a real target Account (deferred to user-run acceptance, see below).

## The three history channels

### 1. MP backend `appmsgpublish` — FULL history, but retired (no network)

- `src/core/mp-client.ts:13` (`APPMSG_PUBLISH = .../cgi-bin/appmsgpublish`): paginated full "已发表" list, 20 publish-groups per page, cursor by group count not article count (`fetchPage`, `listArticles`, `listArticlesSince`).
- Filters reader-inaccessible entries at list time (`isReaderVisible`: `is_deleted`/`checking`/`ban_flag`), stable identity via `appmsgid`/`itemidx` (= mid/idx).
- RETIRED: `src/core/retired-private-api.ts:11` — `RETIRED_PRIVATE_API_COMMANDS = ['crawl']`; `isRetiredPrivateRequest` hard-blocks `auth-verify` / `account-search` / `article-list` at the gateway transport layer. Production entry points make zero network calls on this channel (pinned by `tests/core/retired-private-api.test.ts`, 4/4 passing).
- Verdict: code intact, not callable. Full-history sync via this channel is unavailable in v1.

### 2. Weread mobile list `web/mp/articles` — server-side per-account gating (-2041)

- `src/core/weread/client.ts:41` (`listMpArticles`): offset-paged, 20 per page; `-2041` means list blocked / needs full cookie.
- Upstream `AGENTS.md` records the 2026-08-27 three-round spike as terminal: fresh QR login in a real Chrome window (healthy identity, renewal rotating) still got `-2041`. Cause is server-side account+endpoint gating, not client fingerprint. Upstream decision: "按公众号批量下载历史 N>1 在本产品内无解", do not re-drill.
- Verdict: path exists in code, effectively dead for history. Do not spend v1 effort here.

### 3. Weread web `/api/mp/cover` — LIVE, latest ONE article only

- `src/core/weread/client.ts:34` (`getLatestArticle`) + `parse-articles.ts:50` (`parseCover`): returns exactly one article (reviewId/title/url/coverUrl/accountName), no publish time.
- `src/core/check-subscriptions.ts:43-50`: cover sources use an identity cursor (`latestArticleId`), not a time watermark — a check yields "1 new" or "none".
- `src/core/subscription-digest.ts:77` coverage note (user-visible): each Account only yields its latest article per refresh; articles overwritten between refreshes can be missed.
- Verdict: this is the only live sync channel. Retrievable depth = **latest-only (1 article per check)**.

## Unaffected: per-URL download

`download --url <article link>` parses and downloads any known article URL (title/text/media/metadata, `download-article.ts`, 16/16 tests passing). Depth limits apply to *discovery* (finding old articles), not to *downloading* a known URL. URL-list batch (`--urls-file`) works without any list API.

## Verdict for the spec

| Claim | Status |
|---|---|
| Sync full Account history | NOT retrievable — stage behind upstream unblock |
| Sync latest article per Account (incremental) | Live via `/api/mp/cover` |
| Download any known URL / URL list | Live via per-URL path |
| Two-level picker, archive shape, dedupe, CLI parity | Unaffected (operate on whatever the list yields) |

## What v1 becomes

Staged scope: **latest-only incremental sync + per-URL/URL-list download + full picker/archive/library UX on top**. The sync list in v1 usually holds the latest article per Account plus anything downloaded by URL. Full-history backfill is explicitly staged, not dropped — the `appmsgpublish` code stays as-is so a future unblock only re-opens the gate.

## Deferred live verification (needs owner)

Run with the owner's WeChat QR scan in an isolated library: `subscription check-now` → expect ≤1 new article per Account; `download --url` of a known article → full archive. This is milestone acceptance, never CI.
