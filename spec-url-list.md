# Spec: URL-list mode on the Sync page (user-supplied list as the Article list)

## Problem Statement

I want to pick Articles from an Account and download them one by one, but automatic history listing is gated (`profile_ext/getmsg` needs a WeChat client session — proven in `docs/superpowers/spikes/2026-09-19-profile-ext-history.md`). What I CAN do is collect Article URLs from anywhere (search, forwards, reading history) — but today pasting them only starts a blind batch download with no list, no titles, no archived marks, and no per-Article choice.

## Solution

A URL-list mode on the Sync page: paste multiple Article URLs (or import a txt file), each resolves to a row with title, Account, type, and already-archived mark; the existing Two-level picker (batch type toggles plus per-article checklist) computes the download set live; download runs the existing queue with progress, per-row results, cancel, and retry. Same flow as subscription rows, only the row source differs.

## User Stories

1. As a reader, I want to paste several Article URLs and see one row per URL with title and Account, so that I confirm what will download before it starts.
2. As a reader, I want invalid or duplicate URLs flagged inline (not silently dropped), so that I can fix the list.
3. As a reader, I want each row to show its already-archived mark, so that I don't re-download knowingly.
4. As a reader, I want batch type toggles over URL-list rows, so that I select "all audio" in one click.
5. As a reader, I want a per-article checklist over URL-list rows, so that I download exactly the Articles I tick.
6. As a reader, I want the computed set (count + rows) live before download, so that what I tick is what downloads.
7. As a reader, I want download progress per Article with cancel and single-row retry, so that one bad URL never blocks the batch.
8. As a reader, I want unavailable URLs (deleted, banned) distinguished from transient failures in row results, so that I know what to retry and what to give up on.
9. As a reader, I want to import a txt file of URLs as well as paste, so that long lists don't clog the textbox.
10. As a reader, I want rows from mixed Accounts in one list, so that one batch covers everything I collected.
11. As a power user, I want the same list downloadable headlessly via CLI (`--urls-file` plus a resolve/list step), so that agents can drive it.
12. As a maintainer, I want URL resolution covered by pure tests, so that WeChat URL形态 changes surface as test failures.

## Implementation Decisions

- **New row source, reused everything else.** URL-list mode adds a resolver (URL text → per-URL rows via existing `search --url` logic: `extractArticleKeys` + `parseAccount` + Account confirm) that feeds the SAME row pipeline (`buildSyncRows`, `mergeSyncRows`, `computePicked`, download wiring). No new picker, queue, or archive code.
- **Row identity is `refId` (mid_idx preferred, normalized URL fallback)** — same as subscription rows, so a URL-list row and a subscription row for the same Article dedupe and share archived marks.
- **Resolution is per-URL and honest.** Each URL resolves to ok (title/Account/type), invalid (not an Article URL / unreadable page), or duplicate (same `refId` as another row). Invalid rows stay visible with reasons; duplicates collapse with a note.
- **Library cross-check for archived marks.** Same canonical-id-first, normalized-URL-second rule as subscription rows.
- **Download path is the existing per-URL pipeline** (`downloadArticle` + `DownloadQueue`), so audio/video/images/metadata behave identically; `--no-audio`/`--no-video`/settings flow through unchanged.
- **CLI parity.** A resolve/list step over `--urls-file`/`--url` prints the row list as JSON (shapes mirror the pick preview); download consumes the same file. New flags, not new commands.
- **GUI placement.** URL-list mode lives on the Sync page beside Seed/Account entries (a mode switch, not a new page); renderer only calls `window.api`, never core.
- **No history-list dependency.** This spec explicitly does not require `profile_ext`/`getmsg`; when/if a session-based list lands, its rows plug the same pipeline.

## Testing Decisions

- **What makes a good test:** assert externally observable behavior — resolve results, row lists, JSON shapes, files on disk — never internal regex or helper state.
- **Seam 1 (pure resolve):** URL text in (mixed valid/invalid/duplicate/deep-URL) → per-URL results out, zero network.
- **Seam 2 (CLI):** import file over stubbed article fetch → JSON row list with archived marks → download lands fixtures; `--no-audio` honored.
- **Seam 3 (GUI e2e):** paste URLs into Sync URL-list mode → rows with marks → uncheck shrinks set → download lands fixtures in isolated library.
- **Live acceptance (milestones only, never CI):** the deep-URL acceptance Article resolves to a titled row and downloads with audio.
- **Prior art:** `search-account.test.ts` (resolve), `sync-list.test.ts` (rows), `download-archive.test.ts` (CLI batch), `gui.e2e.mjs` sync block.

## Out of Scope

- Automatic history listing (`profile_ext`/`getmsg` — blocked on client session, see spike).
- Full-text search, URL discovery, or recommendations.
- Changing picker/queue/archive semantics (reuse as-is).
- Video-account (finder) cards and TTS tracks (separate specs).

## Further Notes

- Glossary (`CONTEXT.md`): Account, Seed URL (one URL identifies an Account; here many URLs ARE the list), Article, Whole-article archive, Two-level picker, Login Session (not needed — resolution and download of public Articles are anonymous).
- ADRs 0001–0003 apply; no new ADR — this is a new row source for the existing picker, not a direction change.
- Spike evidence: `docs/superpowers/spikes/2026-09-19-profile-ext-history.md` (why auto-list is out), `docs/superpowers/spikes/2026-09-18-wechat-voice-download.md` (anonymous download precedent).
- Live acceptance Article (deep URL): `https://mp.weixin.qq.com/s/fwjkOAE3_Li15CkK4ZmXvw`.
