# WeChat Downloader

Local-first archive of a WeChat 公众号: sync whole-account history, then download selected articles with media.

## Language

**Account**:
A WeChat 公众号 (official account) being archived. One sync target at a time.
_Avoid_: 公众号名称 alone, biz, MP

**Seed URL**:
Any one article URL from the target Account, pasted by the user to identify which Account to sync.
_Avoid_: account name, WeChat ID, manual biz

**Article**:
One published post from an Account, with text, images, audio, video, and metadata.
_Avoid_: post, message, media item

**Whole-article archive**:
The full Article payload: text + images + audio + video + metadata, preserved for search and re-use.
_Avoid_: media-only dump, screenshot

**Two-level picker**:
The selection UI: type toggles (text/images/audio/video applied to the batch) plus a per-article checklist override.
_Avoid_: single filter, select-all only

**Login session**:
WeChat login state captured by scanning a QR code, saved as a cookie for the history API.
_Avoid_: manual cookie paste, password login
