---
type: change
project: Wapso-Whatsapp-Inbox
status: recorded
owner: engenio
updated: 2026-11-10
source: https://github.com/juancarlord/Wapso/commit/fc4da0c
---

# 2026-10-19 `fc4da0c` Roles, brand UI, media proxy

What: Roles, sender labels, brand UI, dark mode, unread dots, notifications, optimistic send, media cards, a sealed media proxy, and an image viewer. Why: that is the commit subject. On Wapso `main`, ancestor of `kapso-exit`. Not in the WhatsappCloudInbox checkout.

Link: https://github.com/juancarlord/Wapso/commit/fc4da0c

Author date: 2026-10-19 17:26 America/Bogota. No pull request.

`git diff --stat`: 34 files, +1609 / −615. Largest: `src/components/message-view.tsx`, `src/components/image-viewer.tsx` (new), `src/components/conversation-list.tsx`, `src/lib/db.ts`, `src/lib/media-token.ts` (new), `src/lib/media-text.ts` (new), `src/app/api/media/u/[token]/route.ts` (new), `src/app/globals.css`.

- Unread state uses `conversation_reads` (per user, epoch ms).
- Outbound messages can store a staff sender in `message_senders`. The label is inbox-only.
- Media bytes for older Kapso URLs go through `/api/media/u/[token]` instead of handing the browser a raw provider URL.
- Image viewer adds zoom, pan, and rotate (`src/components/image-viewer.tsx`).
- Dark mode and a notifications toggle land in the shell.
- `scripts/test-media-text.mts` is added and wired as `npm run test:media-text`.

Risks: `message-view.tsx` is still one large component after this edit. A bug in the sealed media token would 404 old attachments or, if the token check were weak, expose files. This dump did not review the token construction line by line.

How tested: `npm run test:media-text` was not run in this dump. See [[Commands]].
