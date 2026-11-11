---
type: change
project: Wapso-Whatsapp-Inbox
status: recorded
owner: engenio
updated: 2026-11-10
source: https://github.com/juancarlord/Wapso/commit/610fb4c0c11e526837dc0461c0118075da885ed3
---

# 2026-10-23 `610fb4c` Meta-direct inbox

What: Own Meta webhook, local SQLite message store, and a Kapso export script. Why: commit subject "Meta-direct inbox: own webhook, local store, Kapso export (kapso-exit cutover)". This commit is on `kapso-exit` and on `feature/p009-kc-cutover`. It is not on Wapso `main`.

Link: https://github.com/juancarlord/Wapso/commit/610fb4c0c11e526837dc0461c0118075da885ed3

Author date: 2026-10-23 09:35 America/Bogota. No pull request of its own. Parent of the KC1 squash.

`git diff --stat`: 41 files, +2047 / −67. New areas: `CUTOVER.md`, `src/app/api/webhooks/meta/route.ts`, `src/lib/inbox-store.ts`, `src/lib/inbox-read.ts`, `src/lib/meta-webhook.ts`, `src/lib/outbound.ts`, `src/lib/media-store.ts`, `src/lib/media-tier.ts`, `src/lib/bucket.ts`, `src/instrumentation.ts`, `scripts/kapso-export.mts`, `scripts/media-tier.mts`, `scripts/backup.sh`, `tests/webhook.test.ts`, `tests/media-tier.test.ts`, `tests/lib.test.ts`.

- `INBOX_SOURCE=local` switches the client to `https://graph.facebook.com` and reads lists from SQLite. `kapso` stays the default.
- Webhook `POST /api/webhooks/meta` checks the HMAC and is excluded from the session middleware. Inserts are idempotent on `wa_message_id`. Statuses only move forward.
- `npm run kapso:export` pulls Kapso conversations into the local store. `CUTOVER.md` describes a quiet-window order and says the branch was not pushed. The branch is on GitHub now. See [[Quirks]].
- Media can stay on disk or move to a private bucket after `MEDIA_HOT_DAYS`. The local file is deleted only after the object size and MD5 match.
- Startup checks required env names when `INBOX_SOURCE=local` (`src/instrumentation.ts`).

Risks: exporting media fills `MEDIA_DIR` before tiering (the runbook's 28 GB figure). Rolling back to Kapso hides messages that were received only in local mode (`CUTOVER.md`). Webhook verify token and Graph token are env vars; they are not in git.

How tested: the tests added here are part of `npm test` on the branch tip. Run on 2026-11-11 at `9c4c328` (this commit plus KC1): 45 passed, 0 failed. The export script was not executed.
