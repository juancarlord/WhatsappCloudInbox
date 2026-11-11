---
type: codebase-map
project: Wapso-Whatsapp-Inbox
status: active
owner: engenio
updated: 2026-11-10
source: https://github.com/juancarlord/WhatsappCloudInbox/commit/3db5660a53fe90ef693cf4995bdf648bd4187604
---

# Codebase map

See [[README]]. Two trees. The first is the checkout. The second is the related repo, key modules only.

## Checkout: juancarlord/WhatsappCloudInbox @ `3db5660`

Commit `3db5660a53fe90ef693cf4995bdf648bd4187604`, 2025-11-14 11:54 America/Bogota, author juancarlord, message "Files up". 48 files. No `.github` workflows, no Dockerfile, no tests, no database.

### Tree

```
components.json
eslint.config.mjs
LICENSE
next.config.ts
next-env.d.ts
package.json
package-lock.json
postcss.config.mjs
public/file.svg
public/globe.svg
public/next.svg
public/vercel.svg
public/window.svg
README.md
src/app/api/conversations/route.ts
src/app/api/media/[mediaId]/route.ts
src/app/api/messages/[conversationId]/route.ts
src/app/api/messages/interactive/route.ts
src/app/api/messages/send/route.ts
src/app/api/templates/route.ts
src/app/api/templates/send/route.ts
src/app/favicon.ico
src/app/globals.css
src/app/layout.tsx
src/app/page.tsx
src/components/conversation-list.tsx
src/components/interactive-message-dialog.tsx
src/components/media-message.tsx
src/components/message-view.tsx
src/components/template-parameters-dialog.tsx
src/components/template-selector-dialog.tsx
src/components/ui/   (avatar, badge, button, card, dialog, input, label, scroll-area, separator, skeleton, textarea)
src/hooks/use-auto-polling.ts
src/lib/template-parser.ts
src/lib/utils.ts
src/lib/whatsapp-client.ts
src/types/whatsapp.ts
tsconfig.json
```

`README.md` links to `.github/screenshots/kapso-whatsapp-inbox.png`. That path is not in the tree.

### Entry points

- `src/app/layout.tsx` sets the document title "WhatsApp inbox".
- `src/app/page.tsx` is the only page: client component, two panes (`ConversationList` + `MessageView`).
- `src/lib/whatsapp-client.ts` builds `WhatsAppClient` from `@kapso/whatsapp-cloud-api` `^0.0.6` with `baseUrl: 'https://app.kapso.ai/api/meta/'` and `kapsoApiKey: process.env.KAPSO_API_KEY`.
- API routes under `src/app/api/` are the server. None call an auth helper. There is no `middleware.ts`.

| Method | Path | File | Behavior at `3db5660` |
|---|---|---|---|
| GET | `/api/conversations` | `src/app/api/conversations/route.ts` | `conversations.list`. Query `status` (`active` or `ended`), `limit` default 50, max 100. Returns `data` plus Kapso `paging`. |
| GET | `/api/messages/[conversationId]` | `src/app/api/messages/[conversationId]/route.ts` | `messages.listByConversation`. `limit` default 50, max 100. |
| POST | `/api/messages/send` | `src/app/api/messages/send/route.ts` | `multipart/form-data`: `to`, `body`, optional `file`. Uploads media, then sends image, video, audio, or document. Text if no file. |
| POST | `/api/messages/interactive` | `src/app/api/messages/interactive/route.ts` | JSON `phoneNumber`, `body`, `buttons` (1–3). Button title cut to 20 characters. Optional text `header`. |
| GET | `/api/templates` | `src/app/api/templates/route.ts` | `templates.list` for `WABA_ID`, limit 100. 500 if `WABA_ID` is missing. |
| POST | `/api/templates/send` | `src/app/api/templates/send/route.ts` | Sends a template with parameters. |
| GET | `/api/media/[mediaId]` | `src/app/api/media/[mediaId]/route.ts` | Metadata, then download with `auth: 'never'`. |

### Data model

No tables. Conversation and message objects are mapped in the route handlers from Kapso records (`id`, `phoneNumber`, `status`, `lastActiveAt`, Kapso extensions such as `contact_name`, `last_message_text`, message `direction` / `status` / media fields). Template shapes are in `src/types/whatsapp.ts` (`Template`, `TemplateComponent`, `ParameterFormat` `POSITIONAL` or `NAMED`).

### Env var names (this checkout)

`PHONE_NUMBER_ID`, `KAPSO_API_KEY`, `WABA_ID`. No `.env.example` in the tree. Names are in `README.md` and `src/lib/whatsapp-client.ts` / `src/app/api/templates/route.ts`.

## Related repo: juancarlord/Wapso (not a full dump)

### `main` @ `e84601c` (shipped Kapso line)

Still a Kapso client. `src/lib/whatsapp-client.ts` uses `baseUrl: 'https://api.kapso.ai/meta/whatsapp'` and `graphVersion: 'v24.0'`. SDK range `@kapso/whatsapp-cloud-api` `^0.1.0`. `engines.node` is `>=22.5`.

Entry points added on top of the template: `src/middleware.ts` (session on every page and `/api/*` except `/login` and `/api/auth/login`), `src/app/login/page.tsx`, `src/app/cambiar-contrasena/page.tsx`, `src/app/admin/usuarios/page.tsx`, `src/app/admin/metricas/page.tsx`.

Key modules: `src/lib/db.ts` (SQLite `node:sqlite`, file `$DATA_DIR/inbox.db`), `src/lib/auth.ts`, `src/lib/session.ts`, `src/lib/password.ts`, `src/lib/assignments.ts`, `src/lib/contact-key.ts`, `src/lib/conversation-search.ts`, `src/lib/media-token.ts`, `src/lib/releases.ts`.

SQLite tables created in `src/lib/db.ts`: `users` (`admin` / `lead` / `agent`), `conversation_reads`, `message_senders`, `conversation_assignments` (`open` / `resolved`). `src/lib/conversation-search.ts` adds `conversation_history` for the full-history search (commit `5dafddb`). Conversations and message bodies on this branch are still read live from Kapso, not stored as the inbox of record.

Env names in `.env.example` on `main`: `PHONE_NUMBER_ID`, `KAPSO_API_KEY`, `WABA_ID`, `AUTH_SECRET`, `APP_ORIGIN`, `DATA_DIR`, `ADMIN_USERNAME`, `ADMIN_PASSWORD_HASH`, `ADMIN_NAME`. `src/middleware.ts` also reads `AUTH_INSECURE_COOKIE` (refused in production).

### `kapso-exit` @ `9c4c328` (cutover line)

`CUTOVER.md` at this SHA: `INBOX_SOURCE=kapso` keeps today's Kapso behavior; `INBOX_SOURCE=local` uses `POST /api/webhooks/meta`, SQLite, and `graph.facebook.com`. `src/lib/whatsapp-client.ts` picks `graphClient` when local, `kapsoClient` otherwise. Webhook path skips the session middleware; the route checks the Meta HMAC.

Extra modules: `src/lib/inbox-store.ts`, `src/lib/inbox-read.ts`, `src/lib/meta-webhook.ts`, `src/lib/outbound.ts`, `src/lib/shift.ts`, `src/lib/after-hours.ts`, `src/lib/media-store.ts`, `src/lib/media-tier.ts`, `src/lib/media-source.ts`, `src/lib/bucket.ts`, `src/lib/config.ts`, `src/lib/alert.ts`, `src/lib/log.ts`, `src/lib/send-rules.ts`, `src/instrumentation.ts`, `src/app/api/admin/shift/route.ts`, `src/components/shift-card.tsx`, `scripts/kapso-export.mts`, `scripts/media-tier.mts`, `scripts/backup.sh`, `tests/*.test.ts`.

Local store tables in `ensureInboxSchema` (`src/lib/inbox-store.ts`): `contacts`, `conversations`, `messages`, `statuses`, `meta_events`, `kv`, `webhook_errors`. KC1 adds columns `conversations.off_shift_since`, `conversations.off_shift_cutover`, and table `auto_replies` (`contact_key`, `period_start`) in `src/lib/shift.ts`. Media columns `media_storage`, `media_object_key`. User/assignment tables from `main` are still there (`src/lib/db.ts` is in the cutover diff).

`listLocalConversations` (`src/lib/inbox-read.ts`) selects the latest conversations by `last_message_at` with `LIMIT` (the route passes 100). It returns `offShiftSince` on each row. It has no `WHERE off_shift_since IS NOT NULL`. The Fuera tab filters in the browser. See [[Features]] and [[Decisions]].

Env names added on this branch (`.env.example` and `CUTOVER.md`): `INBOX_SOURCE`, `META_ACCESS_TOKEN`, `META_APP_SECRET`, `META_WEBHOOK_VERIFY_TOKEN`, `META_GRAPH_VERSION`, `MEDIA_DIR`, `MEDIA_BUCKET_URL`, `MEDIA_BUCKET_ACCESS_KEY_ID`, `MEDIA_BUCKET_SECRET_ACCESS_KEY`, `MEDIA_BUCKET_REGION`, `MEDIA_HOT_DAYS`, `ALERT_EMAIL_TO`, `ALERT_EMAIL_FROM`, `ALERT_RESEND_API_KEY`, `ALERT_WEBHOOK_URL`, `BACKUP_TARGET_URL`, `BACKUP_ACCESS_KEY_ID`, `BACKUP_SECRET_ACCESS_KEY`, `BACKUP_AGE_RECIPIENT`, `BACKUP_PASSPHRASE`. Values are not copied here.
