# Spec: WeChat article voice-audio download (mp-common-mpaudio)

## Problem Statement

I archive valuable WeChat Account Articles, and the audio is the core for me — e.g. English-lesson Articles where each track (vocabulary, activities) is an embedded voice player. Today the downloader saves text and images but silently drops every audio track: no files, no player, no warning. My archive is incomplete exactly where it matters most.

## Solution

Extract every voice track from an Article page and download it as local mp3, mirroring the existing video flow: parse `voice_encode_fileid` from each `mp-common-mpaudio` element, resolve via the anonymous `res.wx.qq.com/voice/getvoice` endpoint, save under `audios/audio-N.mp3`, embed an inline `<audio>` player in html and a playable link in markdown, record per-track metadata, and warn honestly on any failure. A `--no-audio` flag and the picker's `audio` toggle control it.

## User Stories

1. As a reader, I want voice tracks in an Article to download as mp3 files next to the text, so that my archive plays offline.
2. As a reader, I want each downloaded track to keep its title and duration, so that I know which lesson part it is.
3. As a reader, I want the saved html to contain a working audio player per track, so that I can listen while reading.
4. As a reader, I want the saved markdown to link each track, so that agents and editors can find the audio.
5. As a reader, I want Articles without audio to behave exactly as before, so that nothing regresses.
6. As a reader, I want a failing track (deleted by author, still transcoding) to warn rather than kill the Article, so that one bad track never blocks text and images.
7. As a reader, I want `--no-audio` to skip audio downloads, so that I can save bandwidth and disk like `--no-video` does.
8. As a reader, I want the picker's audio toggle to actually switch audio downloading, so that the Two-level picker choice is honored (today the `audio` key is a no-op placeholder).
9. As a reader, I want per-track metadata (voice id, title, duration, local path) in the archive metadata, so that I can audit and cite tracks.
10. As a reader, I want expired or unresolvable track URLs to never be persisted, so that the archive never points at dead signed links.
11. As a power user, I want audio failures distinguished from Article failures in CLI output, so that triage doesn't chase dead tracks.
12. As a maintainer, I want audio parsing covered by fixture tests, so that WeChat template changes surface as test failures, not silent drops.

## Implementation Decisions

- **Mirror the video flow, five cuts.** New audio parse module extracting `MpAudioSource` list from page html; `ParsedArticle` gains an `audios` list and archive metadata gains audio records (both without persisted URLs, same rule as video signed links); new audio exporter module downloading to `audios/audio-N.mp3` with html/md suffixes; exporter input gains a `downloadAudios` switch defaulting to true; download deps and CLI gain the matching flag.
- **Canonical key is the element's `voice_encode_fileid`.** The `voiceList` JSON only supplements (`listen_id` fallback, count check); the `readtemplate audio_tmpl` src is display-only and must be ignored. Join semantics follow the player's own matching (base64/`&#61;` variants).
- **Anonymous resolution, single param.** `GET res.wx.qq.com/voice/getvoice?mediaid=<id>` with redirect following yields mp3 bytes; no cookie, Referer, token, or Account identity needed. Verified live against the acceptance Article (two tracks, 0.5MB/1.6MB, `audio/mpeg`).
- **Same-session download, never persisted URLs.** The 302 `filekey` signature is short-lived; resolve and download in one flow, store only voice id/title/duration/local path.
- **No new message kind.** Audio cards live inside normal rich-text Articles as attached content, exactly like the established video semantics — no parser dispatch change.
- **No new request-governor kind.** Reuse the article-asset throttle; audio files are sub-MB and anonymous requests show no gating.
- **Timeout follows the image tier (30s), not the video volume-scaled tier.** Audio tracks are hundreds of KB, not hundreds of MB.
- **State-aware skip.** Tracks flagged unplayable/deleted/transcoding in element state attributes are warned, not fetched.
- **Picker wiring.** The `audio` key in the pick selection becomes a real download-dimension switch; row filtering stays text/video only, per the existing documented rule.
- **GUI Sync page.** The audio toggle rides the existing type toggles; no new page work in this spec.

## Testing Decisions

- **What makes a good test:** assert externally observable behavior — parsed voice lists, files on disk, suffix content, metadata records, CLI JSON — never internal regex or helper state.
- **Seam 1 (pure parse):** fixture html with `mp-common-mpaudio` elements plus `voiceList` yields voice ids, titles, durations; dedupe across the two content copies; unplayable-state tracks warn without fetching.
- **Seam 2 (exporter):** stubbed binary fetch lands `audios/audio-N.mp3`, html suffix with inline player, markdown suffix with links, metadata records without URLs; failure yields warnings, not thrown errors.
- **Seam 3 (CLI):** fixture download lands the archive with audio; `--no-audio` skips with a note; JSON summary distinguishes audio warnings.
- **Live acceptance (milestones only, never CI):** the acceptance Article resolves to exactly 2 landed mp3 tracks.
- **Prior art:** the video test suites at the same three levels are the template; extend, don't invent a harness.

## Out of Scope

- Video-account (finder) cards and TTS read-aloud tracks — different chains, separate specs.
- Transcription, waveform rendering, or chapter splitting of audio.
- Audio search, playlist UI, or streaming server.
- Retroactive backfill of previously downloaded Articles (re-download picks them up).
- Changing row-filter semantics of the picker (still text/video only).

## Further Notes

- Evidence: `docs/superpowers/spikes/2026-09-18-wechat-voice-download.md` (endpoint verified live, both tracks fetched anonymously).
- Glossary (`CONTEXT.md`): Article, Whole-article archive (now truly whole: text + images + audio + video + metadata), Two-level picker. ADR-0001 (reuse wx-kit) and ADR-0003 (two-level picker) apply; no new ADR — this fills the archive definition rather than changing direction.
- Acceptance Article: `https://mp.weixin.qq.com/s/fwjkOAE3_Li15CkK4ZmXvw` (2 voice tracks expected on disk).
- Next step: `/to-tickets` slices this (parse, exporter, CLI/picker wiring, acceptance), then `/implement` per ticket with `/tdd` at the agreed seams.
