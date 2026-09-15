# Spec: WeChat Account Archive Downloader (wx-kit fork)

## Problem Statement

I follow valuable WeChat 公众号 posts (audio, video, full articles) and I am afraid of losing them: links rot, history is hard to browse, and media files are scattered across WeChat CDN URLs with expiring signatures. I want to type one article link, sync the whole Account history, pick what I actually want (by media type and by individual Article), and keep a local Whole-article archive I can search, read, and re-use — from an app I can operate with a mouse, logging in by QR scan rather than fiddling with cookies.

## Solution

Fork and extend wx-kit (Electron + TypeScript desktop app with GUI + JSON CLI) into a local-first Account archiver: paste a Seed URL to identify the Account, scan a QR to establish a Login Session, sync the Account history list, choose content through a Two-level picker (batch type toggles for text/images/audio/video plus a per-article checklist), then download each selected Article as a Whole-article archive (text + images + audio + video + metadata) into a local file-system library with JSON index.

## User Stories

1. As a reader, I want to paste a Seed URL from the Account I care about, so that the tool knows which Account to sync without me finding internal IDs.
2. As a reader, I want the tool to resolve the Account identity (name, id) from the Seed URL and show it back to me, so that I can confirm I targeted the right Account before a long sync.
3. As a reader, I want to scan a QR code in my WeChat app to log in, so that I don't have to copy cookies from devtools.
4. As a reader, I want the Login Session to persist between runs (with a visible logged-in / expired state), so that I don't re-scan every day.
5. As a reader, I want to be told honestly when the Login Session expires and re-login from the same screen, so that I never mistake "need login" for "no new articles".
6. As a reader, I want to sync the full history list of the Account (not just recent posts), so that old valuable Articles are archivable.
7. As a reader, I want each history entry to show title, publish time, type, and already-archived state, so that I can decide what to download.
8. As a reader, I want batch type toggles (text / images / audio / video) applied to the whole sync result, so that I can say "all audio" in one click.
9. As a reader, I want a per-article checklist over the synced list, so that I can include or exclude specific Articles regardless of the batch toggles.
10. As a reader, I want the Two-level picker to combine both levels predictably (toggles set defaults, checklist overrides), so that the final download set matches what I ticked.
11. As a reader, I want to download every selected Article with its media localized (images/audio/video saved next to the text), so that the archive survives expiring CDN URLs.
12. As a reader, I want failures (unavailable, paywalled, deleted, network) recorded per Article with a clear reason instead of silent success, so that I know what to retry and what to give up on.
13. As a reader, I want re-runs to skip already-archived Articles (identity-stable dedupe), so that syncing twice doesn't duplicate or re-download everything.
14. As a reader, I want a local library I can browse, search by keyword, filter by Account, read, delete, and reveal in folder, so that the archive is usable after downloading.
15. As a reader, I want each archived Article to keep its metadata (Account, author, publish time, source URL, type, warnings), so that I can cite and audit it later.
16. As a reader, I want download progress and per-article results visible in the GUI, so that a hundred-article batch doesn't feel dead.
17. As a reader, I want to cancel a running batch and retry failed Articles, so that one bad Article doesn't block the rest.
18. As a power user / agent, I want every GUI capability available as a JSON CLI (stdout JSON, stderr progress, documented exit codes), so that scripts and AI agents can drive sync, pick, and download headlessly.
19. As a maintainer, I want unavailable/removed Articles distinguished from real failures in reports, so that triage doesn't chase dead links.

## Implementation Decisions

- **Base: fork wx-kit, don't rebuild.** Reuse its Electron shell (React renderer via preload API only), main/CLI dispatch, Chromium session/request stack, core parsing/download/library/export modules, and JSON CLI contract. New work is: Seed-URL account resolution UX, history-sync verification against current WeChat limits, and the Two-level picker. (ADR-0001)
- **Account resolution from Seed URL.** The Seed URL is the only v1 identity input; the tool extracts the Account identity from it and confirms it in UI before syncing. No account-name search and no manual internal-ID entry in v1.
- **Login Session via QR scan.** Login state is captured by QR scan and stored as a persisted session for the history API; UI surfaces valid/expired explicitly; auth errors propagate as auth errors and are never downgraded to empty results. (ADR-0002)
- **History sync is list-first, download-second.** Syncing produces a stable, identity-keyed Article list; downloading consumes an explicit selection set. The picker sits between the two phases, never interleaved.
- **Two-level picker semantics.** Batch type toggles define the default selection per media type across the synced list; the per-article checklist overrides per Article; the effective download set is computed once and shown before download starts. (ADR-0003)
- **Whole-article archive shape.** Each archived Article is a directory with human-readable text, a media folder set (images/audio/video as selected), machine-readable metadata including warnings, and a failure log only when something failed — same conventions as the upstream library so export and search keep working.
- **Dedupe by stable Article identity.** Re-syncs and re-downloads match on the canonical Article identity (not display URLs that have short/long forms); already-archived entries are skipped and reported as such.
- **CLI parity.** Sync, selection query, download, library search/remove, subscription-style check, and settings get/set stay available as JSON CLI commands with the existing exit-code contract (success / business failure / usage-or-login error); new picker options are flags, not new commands.
- **Honest failure taxonomy.** Reader-inaccessible Articles (deleted, banned, under review, paywalled) and transient failures (network, rate-limit with backoff) are distinct result classes from warnings (partial media loss); none is reported as silent success.
- **Scope guard from upstream reality.** Upstream wx-kit notes current WeChat list-API restrictions (bulk per-Account crawling paused, subscriptions latest-only). First implementation task is to verify what history depth is actually retrievable for a real target Account and shrink or stage the sync claim to match, rather than assuming full history works.

## Testing Decisions

- **What makes a good test here:** assert externally observable behavior only — CLI stdout JSON shape and exit codes, files present in an isolated library directory, metadata/warnings content — never internal parsing helpers or component state.
- **Main seam (one seam): CLI JSON contract + isolated library on disk.** Drive sync/list/download through the CLI against fixtures or a stubbed transport, assert JSON results and resulting files/metadata. This covers resolution, picker-set computation, dedupe/skip, and failure taxonomy without a GUI.
- **Second surface (narrow): GUI e2e for the Two-level picker only.** Real window tests assert toggles flip the batch default, checklist overrides individual rows, and the computed set shown matches what downloads. No other GUI flows get e2e in v1.
- **Live network: milestones only.** A real Seed URL download runs manually at milestones/release acceptance in an isolated library; it is never a CI gate and fixture results never substitute for it.
- **Prior art:** follow the forked repo's existing suites — unit tests for core, GUI e2e for current screens, live-download script for real-URL acceptance — and extend them rather than inventing a new harness.

## Out of Scope

- Account-name/ID search, manual internal-ID entry, multi-Account sync in one run.
- Auto QR-login without user scan, password login, session sharing across machines.
- Full-text search engine beyond keyword search, tagging, collections, or publishing/site-sync beyond what the fork already has.
- Paywall bypass, private/unreadable content retrieval, read-count/like-count scraping.
- Mobile app, web-hosted service, cloud sync, multi-user support.
- Video transcoding, audio transcription, AI summarization, or creation workflows on top of the archive.

## Further Notes

- Tracker is GitHub Issues via `gh`; this spec could not be published as an issue yet because this folder has no git remote — wire `git init` + GitHub remote first, then publish and apply `ready-for-agent`.
- Glossary (`CONTEXT.md`) terms used throughout: Account, Seed URL, Article, Whole-article archive, Two-level picker, Login Session. Decisions recorded in `docs/adr/0001-reuse-wx-kit.md`, `0002-qr-scan-login-session.md`, `0003-two-level-picker.md`.
- Next step after this spec: `/to-tickets` to split into tracer-bullet tickets (fork setup, session verify, history-depth spike, picker UI, CLI flags, library/dedupe, acceptance), then `/implement` per ticket with `/tdd` at the CLI seam and `/code-review` before commit.
