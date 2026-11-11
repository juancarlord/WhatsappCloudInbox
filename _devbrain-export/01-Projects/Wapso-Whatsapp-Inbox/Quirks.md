---
type: quirks
project: Wapso-Whatsapp-Inbox
status: active
owner: engenio
updated: 2026-11-10
source: https://github.com/juancarlord/WhatsappCloudInbox/commit/3db5660a53fe90ef693cf4995bdf648bd4187604
---

# Quirks

See [[Features]] and [[Decisions]].

## Checkout `3db5660`

- The 24-hour window is enforced only in `src/components/message-view.tsx`. `POST /api/messages/send` and the interactive and template routes do not check it. Anyone who can reach the port can send.
- No auth middleware. `README.md` in this checkout does not mention that. Wapso's later README does.
- `GET /api/conversations` returns Kapso `paging`, and it accepts `status` and `limit`. The UI fetches `/api/conversations` with an empty query, so it always gets the default 50 and never sends `after`. There is no "load more" in `src/components/conversation-list.tsx`.
- Polling pauses while the tab is hidden (`src/hooks/use-auto-polling.ts`). A background tab does not refresh.
- Media download passes `auth: 'never'` and logs `[Media API Test]` plus file sizes (`src/app/api/media/[mediaId]/route.ts`). That log line is a leftover test comment.
- `README.md` says the upstream clone URL is `https://github.com/gokapso/whatsapp-cloud-inbox` and shows a screenshot at `.github/screenshots/kapso-whatsapp-inbox.png`. The screenshot file is not in this commit.
- `README.md` says open port 4000. `npm run dev` uses `-p 4000`. `npm start` is `next start`, which defaults to port 3000.
- Kapso base URL in this file is `https://app.kapso.ai/api/meta/`. On Wapso `main` (`src/lib/whatsapp-client.ts` at `e84601c`) it is `https://api.kapso.ai/meta/whatsapp` with Graph `v24.0`. Same product, different host string.
- SDK pin here is `^0.0.6`. Wapso `main` and `kapso-exit` use `^0.1.0`.
- The route error handlers `console.error` the caught value. If that value ever contains a token or a phone number, it will land in the log. Wapso `kapso-exit` adds `safeError` for this. This checkout does not.

## Wapso `main` @ `e84601c`

- `README.md` opens with "There is no database, no login and no agent/queue system yet." Later in the same file, and in `src/middleware.ts` / `src/lib/db.ts`, login and SQLite users exist. The opening status paragraph is stale. The planned-work paragraph still lists login and roles as future work.
- `CHANGELOG.md` says the customer-facing name is the Tekobo Suite business messaging integration and not to call it "Wapso" in customer-facing wording. The GitHub repo is named Wapso. The npm package is still `whatsapp-cloud-inbox`.
- Conversation poll on this branch is 10 s, or 30 s when the tab is hidden, and it does not pause when hidden (comment: notifications). Message poll stays 5 s. Assignment bar polls every 15 s (`src/components/message-view.tsx`).
- `src/components/message-view.tsx` sets the composer to a hard-coded Spanish canned reply for document verification. That string includes a PBX phone number. The digits are not copied into these notes.
- Changelog deploy line has no deploy run id. Treat "shipped" as a statement in git, not as a Coolify log.

## Wapso `kapso-exit` @ `9c4c328`

- `CUTOVER.md` says the branch is "local branch, not pushed". The branch is on `origin` as of 2026-11-11. That sentence is stale.
- Office-hours auto-reply and the Fuera flag run only when `INBOX_SOURCE=local`. Deploying this branch with the default Kapso source does not turn the queue on.
- The Fuera tab sorts oldest first, but only among chats the list endpoint already returned (limit 100, ordered by last message, not by `off_shift_since`). See [[Decisions]] (MED-1).
- Close is exclusive. 17:00:00 Monday and 12:00:00 Saturday are closed. Tests in `tests/shift.test.ts` lock that.
- A settings row already stored in `kv` is not rewritten when defaults change. PR #1 notes say saved `days` stay as stored. Confirmed by `getShift`: it returns the validated JSON, not `DEFAULT_SHIFT`, when the row parses.
- Auto-reply send is one attempt. Failure alerts and does not retry (`src/lib/after-hours.ts`). The claim row stays, so a later message in the same closed period does not send a second text.
- `kapso-exit` does not contain `5dafddb` or `19c290b`. Full-history search from 2026-11-09 is on `main` only. Merging the cutover to `main` is not done and will conflict. `UNVERIFIED` how large that conflict is; the merge was not attempted in this session.
- `feature/p009-kc-cutover` is still on the remote after the squash merge. It is not the tip of the work. The tip of KC1 is `kapso-exit` `9c4c328`.
