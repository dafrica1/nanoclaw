# NanoClaw Migration Guide

Generated: 2026-06-28
Base: 934f063aff5c30e7b49ce58b53b41901d3472a3e
HEAD at generation: 1bc2b5e6954141556fe30eaf92b2f0a9abda8c5f
Upstream: 2afbd1823356a610302cc13e95f87204d3413d43

## Applied Skills

- `add-telegram` — was built directly into v1 `src/channels/telegram.ts` using Grammy.
  In v2, use the upstream `/add-telegram` skill (merges `upstream/skill/add-telegram` or
  the channels branch equivalent). Do NOT copy the v1 telegram.ts — it is incompatible
  with v2's Chat SDK bridge architecture.

## Customizations

### Enable Telegram topic/thread support

**Intent:** Replies from the agent go back to the same Telegram supergroup topic the user
messaged from (via `message_thread_id`). The v1 code supported this; v2 ships with it
disabled by default.

**Files:** Wherever the v2 add-telegram skill sets up the bridge config — look for
`supportsThreads` in `src/channels/telegram.ts` after applying the skill.

**How to apply:** After running `/add-telegram`, find the bridge config object (the argument
to the Chat SDK bridge factory) and change:
```typescript
supportsThreads: false,
```
to:
```typescript
supportsThreads: true,
```

### Add image vision support

**Intent:** Photos sent via Telegram are processed as multimodal content by the agent
(resized and passed as image content blocks). This replaces the v1 file download approach
for images.

**How to apply:** Run `/add-image-vision` after upgrading. No custom code needed.

### Add voice transcription support (optional)

**Intent:** Voice notes sent via Telegram are transcribed via Whisper and sent as text to
the agent. This replaces the v1 voice file download approach.

**How to apply:** Run `/add-voice-transcription` after upgrading. No custom code needed.

## What was intentionally dropped

- **Raw file downloads for video/audio/documents** — the v1 code downloaded these to
  `group/attachments/` using Grammy's `getFile()` API. In v2, the Chat SDK adapter
  handles file metadata (captions, type, duration) without downloading binaries.
  This is acceptable — the agent sees file metadata rather than raw bytes.

- **Reply/quoted message context custom code** — v2's Chat SDK bridge already includes
  `extractReplyContext()` which passes `{ text, sender }` to the agent automatically.
  No custom code needed.

- **/chatid and /ping bot commands** — v2 uses a pairing flow instead of manual chat ID
  collection. The commands are no longer needed.
