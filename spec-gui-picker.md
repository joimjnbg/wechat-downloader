# Spec: GUI Sync Picker page (Two-level picker in the renderer)

## Problem Statement

The Two-level picker only exists as a JSON CLI preview (`library export --pick`). I operate the app with a mouse: I want to paste a Seed URL or pick a subscribed Account, see the synced Article list, flip batch type toggles, tick individual Articles, and watch the computed download set before anything downloads — on one page, with QR login state visible.

## Solution

A new top-level Sync page (`/sync`, nav item beside Download/Subscriptions/Library): Seed URL input plus subscribed-Account selector on the same page (both entries together, per grill 2026-09-16); sync runs the existing check pipeline; pending rows render with batch type toggles (text/images/audio/video) plus a per-article checklist; the computed set (count + rows) shows live before download; download consumes the set through the existing queue with progress, cancel, and per-article results.

## User Stories

1. As a reader, I want to paste a Seed URL on the Sync page and see the resolved Account identity inline, so that I confirm the target before syncing.
2. As a reader, I want to pick from my subscribed Accounts on the same page, so that repeat syncs need no URL paste.
3. As a reader, I want the Login Session state (valid/expired) visible on the Sync page with a re-login path, so that I never mistake expiry for "no new articles".
4. As a reader, I want batch type toggles for text/images/audio/video applied to the synced list, so that I select "all video" in one click.
5. As a reader, I want a per-article checklist over the synced rows, so that I include or exclude specific Articles regardless of toggles.
6. As a reader, I want the computed download set (count + rows) shown live before download, so that what I tick is what downloads.
7. As a reader, I want download progress per Article with cancel and retry of failures, so that one bad Article never blocks the batch.
8. As a reader, I want already-archived rows marked as such in the list, so that I don't re-download knowingly.
9. As a power user, I want the page's computed set to match the CLI `library export --pick` output for the same selection, so that GUI and agent paths agree.

## Implementation Decisions

- **New `Sync.tsx` page + `/sync` route + nav entry** in `MainLayout` (same pattern as existing pages). Renderer only calls `window.api` — never imports core directly (hard upstream rule).
- **New IPC pair, thin delegation** (same shape as `subscriptions:*` in `ipc.ts`/`preload.ts`/`api.ts`): one to resolve-and-sync (Seed URL → Account confirm → check pipeline), one to compute the pick set via the existing `applyPick` pure function over subscription pending rows. No new core logic — reuse `applyPick`, `refId`, `runSubscriptionCheck`, `DownloadQueue`.
- **Type mapping v1**: toggles expose text/images/audio/video; row filtering uses text/video two档 (same as `applyPick`); images/audio ride download settings, documented in UI copy.
- **State**: pending rows from `SubscriptionsState.accounts[].newRefs`; selection lives in page state (toggles + include/exclude id sets); computed set memoizes from `applyPick`.
- **Progress/cancel/retry**: reuse `onSubscriptionDownloadProgress` broadcast + existing per-row result/TTL patterns from `Subscriptions.tsx`; cancel via existing `shouldContinue`/AbortSignal path.
- **Out of scope**: full-history backfill (still staged behind upstream unblock); reader/reveal (Library owns them); paywall bypass; mobile/web service.

## Testing Decisions

- **Good tests**: assert externally observable behavior — IPC JSON shapes, rendered rows/toggles/set count, e2e clicks — never component internals.
- **Main seam: new IPC endpoints** — contract tests at `window.api` level (resolve+sync returns Account + rows; pick computation matches CLI preview for same selection).
- **Second surface: GUI e2e on `/sync`** with fixture cover + article pages (same harness as `gui.e2e.mjs`): toggles flip batch, checklist overrides, shown set matches before download; one download-through assertion in isolated library.
- **Live network: milestones only**, never CI.
- **Prior art**: `tests/e2e/gui.e2e.mjs` fixture server (cover + short-link article pages), `tests/electron/subscription-check.test.ts` orchestration pins.

## Out of Scope

- Extending the Subscriptions page instead (grill chose dedicated page).
- Prototype variations (skipped per user: straight to spec).
- Full-text search, tagging, site-sync changes, video transcoding/transcription.

## Further Notes

- Glossary (`CONTEXT.md`): Account, Seed URL, Article, Whole-article archive, Two-level picker, Login Session. ADRs 0001–0003 apply; picker semantics unchanged, only the surface is new.
- CLI contract (`library export --pick/--from-subscription`) is the reference oracle for the computed set.
- Tracker: local files (`.scratch/wechat-downloader/issues/`); publish follow-up tickets there. GitHub publish still blocked on no remote.
