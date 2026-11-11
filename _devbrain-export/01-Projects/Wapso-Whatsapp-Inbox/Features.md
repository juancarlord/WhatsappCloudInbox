---
type: features
project: Wapso-Whatsapp-Inbox
status: active
owner: engenio
updated: 2026-11-10
source: https://github.com/juancarlord/WhatsappCloudInbox/commit/3db5660a53fe90ef693cf4995bdf648bd4187604
---

# Features

See [[README]] and [[Commands]]. "How to test" for the checkout means `npm run dev` and the UI at `http://localhost:4000`. That server was not started here (it needs the three Kapso env vars). Behavior below is from reading the files at the cited SHA.

## In the checkout (`3db5660`)

### Conversation list

What: Loads conversations from Kapso and refreshes on a timer. Selecting one opens the message pane. After a template send, `src/app/page.tsx` refreshes the list and selects the matching phone number.

Path: `src/components/conversation-list.tsx`, `src/app/api/conversations/route.ts`, `src/hooks/use-auto-polling.ts`.

How to test: Open the app. The list calls `GET /api/conversations` with no query (so the route default limit is 50). Poll interval is 10000 ms. The hook stops polling while the browser tab is hidden.

### Message thread and 24-hour window

What: Loads messages for the selected conversation. Free-form send is allowed only when some inbound message has a timestamp less than 24 hours ago. Otherwise the composer tells the user to send a template. The check is in the browser. `POST /api/messages/send` does not repeat it.

Path: `src/components/message-view.tsx` (`isWithin24HourWindow`, poll 5000 ms), `src/app/api/messages/[conversationId]/route.ts`.

How to test: Open a chat whose last inbound message is older than 24 hours. The text box should be replaced by the template prompt. Sending via the API directly is not blocked by this check.

### Text and media send

What: Text, or a file uploaded to WhatsApp media then sent as image, video, audio, or document. Caption is the text body. Audio is sent without a caption.

Path: `src/app/api/messages/send/route.ts`, `src/components/message-view.tsx`.

How to test: Send a text inside the 24-hour window. Attach a small image and confirm the request is `multipart/form-data` with `to`, `body`, and `file`.

### Templates

What: List the WABA's templates, detect positional vs named parameters, fill them, send.

Path: `src/app/api/templates/route.ts`, `src/app/api/templates/send/route.ts`, `src/lib/template-parser.ts`, `src/components/template-selector-dialog.tsx`, `src/components/template-parameters-dialog.tsx`.

How to test: Use "Enviar plantilla" in the composer. With `WABA_ID` unset, `GET /api/templates` returns 500 and `{ error: 'WABA_ID not configured' }`.

### Interactive buttons

What: Reply-button message, optional text header, required body, 1 to 3 buttons, title truncated to 20 characters.

Path: `src/app/api/messages/interactive/route.ts`, `src/components/interactive-message-dialog.tsx`.

How to test: Open the interactive dialog, add three buttons, submit, and confirm the JSON body uses `phoneNumber`, `body`, and `buttons`.

### Media display

What: Incoming media is loaded through `/api/media/[mediaId]`. The route downloads with `auth: 'never'` because the comment says the CDN URL should not get the Kapso auth header.

Path: `src/app/api/media/[mediaId]/route.ts`, `src/components/media-message.tsx`.

How to test: Open a chat that has an image. The browser should request `/api/media/<id>`, not the Kapso host.

### Delivery ticks

What: Outbound bubbles show a failed mark, or one check (sent), or two checks (delivered or read).

Path: `src/components/message-view.tsx` around the `message.status` branch.

How to test: Needs a message whose Kapso status is `failed`, `sent`, `delivered`, or `read`. Not exercised in this session.

### Office hours and Fuera de turno

Not in this checkout. No `shift.ts`, no "Fuera" tab, no `off_shift_since`. The only time window in this tree is the WhatsApp 24-hour customer-care window above. The Mundo office-hours feature is on Wapso `kapso-exit`, next section.

## On Wapso `kapso-exit` @ `9c4c328` (not in the checkout)

### Fuera de turno and office hours (P-009-KC KC1)

What: When `INBOX_SOURCE=local`, an inbound webhook message outside the shift is marked on the conversation (`off_shift_since`, oldest timestamp kept). The inbox tab "Fuera" (full title "Fuera de turno") shows those rows, oldest first. On first load, if any loaded chat has `offShiftSince`, the tab switches to Fuera once. An agent reply (`src/lib/outbound.ts`) or Tomar (`take` in `src/app/api/assignments/route.ts`) clears the flag. The automatic reply does not clear it.

Hours in code (`src/lib/shift.ts` `DEFAULT_SHIFT`): timezone `America/Bogota`, open `07:00`, close `17:00` (exclusive), days Monday–Saturday (`1`–`6`), Saturday close override `12:00`. Sunday is closed because it is not in `days`. `isOpenAt` treats a minute as open when it is `>= open` and `<` that day's close. So Monday 16:59 is open, Monday 17:00 is closed, Saturday 11:59 is open, Saturday 12:00 is closed.

One auto-reply per contact per closed period, and only if the feature is enabled, the message source is `webhook`, the message is not stale, and the inbox is in local mode. The claim row is inserted before the send. The send runs after the webhook responds (`src/lib/after-hours.ts`). Messages older than 10 minutes (`STALE_MS`) are queued with the cutover flag and not auto-replied. Reactions, system, unsupported, and similar types are ignored. `INBOX_SOURCE=kapso` does not run this block (`IS_LOCAL` guard in `src/app/api/webhooks/meta/route.ts`).

The list API does not query the queue. `listLocalConversations` returns up to 100 chats ordered by `last_message_at`, each with `offShiftSince`. `src/components/conversation-list.tsx` filters `c.offShiftSince` in the browser. A Fuera chat outside that 100 is not returned. That is the open server-side queue query. See [[Decisions]].

Admin card "Horario y fuera de turno": `src/components/shift-card.tsx`, `GET`/`PUT` `src/app/api/admin/shift/route.ts` (admin only; the file comment says lead/agent get 403). Settings live in SQLite `kv` key `shift_settings`. Defaults apply when nothing is saved. A saved settings object keeps its own `days`.

Default Spanish and English texts are constants `DEFAULT_TEXT_ES` and `DEFAULT_TEXT_EN` in `src/lib/shift.ts`. They state the same hours and say the channel does not deliver exam results. Not pasted here.

Path: `src/lib/shift.ts`, `src/lib/after-hours.ts`, `src/lib/inbox-read.ts`, `src/app/api/webhooks/meta/route.ts`, `src/components/conversation-list.tsx`, `src/components/shift-card.tsx`, `tests/shift.test.ts`.

How to test: On `kapso-exit` at `9c4c328`, `node --test --experimental-strip-types --no-warnings tests/shift.test.ts`. Run on 2026-11-11 against that tree: 16 tests, 0 failed. The full `tests/*.test.ts` run the same day: 45 passed, 0 failed (matches the count named in [Wapso PR #1](https://github.com/juancarlord/Wapso/pull/1)). A live webhook test needs `INBOX_SOURCE=local` and Meta credentials. That was not run.

The Saturday 07:00–12:00 close is present in `9c4c328` (`dayClose: { '6': '12:00' }`) and in the feature-branch tip `84624c5`. It is not on Wapso `main` @ `e84601c` (no `src/lib/shift.ts`).

### Local inbox (`INBOX_SOURCE=local`)

What: Webhook stores messages in SQLite, media on disk, sends go to the Graph API. List and message routes read the DB. Poll uses a `since` cursor and returns 204 when the change sequence is unchanged. 24-hour window is enforced on send (409 `window_closed`; templates exempt) per `CUTOVER.md` and `WINDOW_MS` in `src/lib/outbound.ts`.

Path: `CUTOVER.md`, `src/lib/inbox-store.ts`, `src/app/api/webhooks/meta/route.ts`, `src/lib/outbound.ts`.

How to test: `npm test` on this branch (45 passed on 2026-11-11, including webhook signature, idempotent insert, and status ordering). Cutover steps in `CUTOVER.md` were not executed.

### Media tier to a private bucket

What: Files whose newest message is older than `MEDIA_HOT_DAYS` (default 7) move from `MEDIA_DIR` to a private bucket. The media route reads local disk first, then the bucket. The local file is deleted only after size and MD5 match. Without the bucket env vars, tiering stays off.

Path: `src/lib/media-tier.ts`, `src/lib/bucket.ts`, `scripts/media-tier.mts`, `CUTOVER.md`.

How to test: `tests/media-tier.test.ts` is part of the 45 that passed. `npm run media:tier -- --dry-run` needs the bucket env vars and was not run.

## On Wapso `main` only (not on `kapso-exit`, not in the checkout)

These landed after the split at `a88bd69`. They are not in `9c4c328`.

### Login, roles, assignment

What: Cookie session, roles `admin` / `lead` / `agent`, user admin, password change, chat assignment and resolved status, unread dots, sender labels. See commits `c6edafd`, `fc4da0c`, `64cf527`.

Path: `src/middleware.ts`, `src/lib/db.ts`, `src/lib/assignments.ts`, `src/app/login/page.tsx`, `src/app/admin/usuarios/page.tsx`.

How to test: Not run here. `npm run hash-password` prints a hash and does not log a password if used as the script intends (`scripts/hash-password.mjs`). Do not paste hashes into notes.

### Full history and history search (2026-11-09)

What: Opening a chat merges older Kapso conversations for that contact. Search checks the loaded list first, then a SQLite index `conversation_history`, with a spinner while the history search runs.

Path: `src/app/api/messages/[conversationId]/route.ts` (`19c290b`), `src/lib/conversation-search.ts` and `src/app/api/conversations/route.ts` (`5dafddb`).

How to test: Not run here. Changelog says this shipped 2026-11-09. Deploy run id was not recorded.

### Metrics and Novedades

What: `/admin/metricas` for leads and admins. "Sin asignar" is the default tab. A Novedades modal reads `src/lib/releases.ts`.

Path: `src/app/admin/metricas/page.tsx`, `src/components/whats-new.tsx`.

How to test: Not run here.
