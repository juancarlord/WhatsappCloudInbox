---
type: change
project: Wapso-Whatsapp-Inbox
status: recorded
owner: engenio
updated: 2026-11-10
source: https://github.com/juancarlord/Wapso/commit/5dafddb
---

# 2026-11-09 `5dafddb` History search

What: Search checks the conversations already loaded, then a full-history index, and shows a spinner while that second query runs. Why: commit subject. On Wapso `main` only. Not on `kapso-exit`.

Link: https://github.com/juancarlord/Wapso/commit/5dafddb

Author date: 2026-11-09 08:54 America/Bogota. No pull request.

`git diff --stat`: 3 files, +721 / −120. Paths: `src/lib/conversation-search.ts` (new, 426 lines), `src/app/api/conversations/route.ts`, `src/components/conversation-list.tsx`.

- A background backfill pages through Kapso conversations into SQLite table `conversation_history`, paced for rate limits (`src/lib/conversation-search.ts`).
- Search order in the changelog: a direct Kapso lookup when the query is a phone number, then the SQLite index, including sibling conversations for the same phone or contact name.
- The list still filters the in-memory rows immediately. The spinner is the full-history request.

Risks: the index is another table in `inbox.db` on the Coolify volume. It stores contact names and phone numbers. Losing the volume loses the index (it can be rebuilt from Kapso while Kapso is still the source). This table does not exist on `kapso-exit`, which has its own `contacts` table instead.

How tested: not run. UI spinner and backfill pacing were not observed. Changelog groups this with `19c290b` as shipped the same day, no deploy run id.
