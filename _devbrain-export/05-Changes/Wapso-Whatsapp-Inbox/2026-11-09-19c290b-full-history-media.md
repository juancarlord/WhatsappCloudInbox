---
type: change
project: Wapso-Whatsapp-Inbox
status: recorded
owner: engenio
updated: 2026-11-10
source: https://github.com/juancarlord/Wapso/commit/19c290b
---

# 2026-11-09 `19c290b` Full history and media fallback

What: Opening a chat loads older Kapso conversations for that contact, and expired media falls back to a fresh download URL. Why: commit subject. On Wapso `main` only. Not on `kapso-exit`.

Link: https://github.com/juancarlord/Wapso/commit/19c290b

Author date: 2026-11-09 08:33 America/Bogota. No pull request. Changelog entry added later the same morning in `6cf6467`.

`git diff --stat`: 5 files, +305 / −139. Paths: `src/app/api/messages/[conversationId]/route.ts`, `src/app/api/media/u/[token]/route.ts`, `src/components/message-view.tsx`, `src/lib/media-text.ts`, `src/lib/media-token.ts`.

- The messages route finds sibling Kapso conversations by phone and merges them, up to 10 pages per conversation, 100 messages per page, in batches of 5, de-duplicated by message id. Counts are from `CHANGELOG.md` at `6cf6467`, which matches the files this commit touches.
- The 5-second poll asks for one page (50) and merges into the messages already on screen. Switching chats drops late responses.
- `/api/media/u/[token]` tries Kapso's stored URL. Meta media ids expire after about 30 days (changelog). If the fetch fails or returns JSON, it tries a fresh Meta download URL for the media id, and only allows listed hosts. Otherwise 502.

Risks: opening a busy contact issues many Kapso GETs. A host allowlist that is too wide would let the server fetch arbitrary URLs; this dump did not re-read every allowlist entry. The behavior is on `main` and absent from the cutover branch, so a future merge has to keep both the local store and this Kapso merge.

How tested: not run in this dump. Changelog says Juan confirmed ship on 2026-11-09 via Rocky, with no deploy run id.
