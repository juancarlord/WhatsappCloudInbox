---
type: decisions
project: Wapso-Whatsapp-Inbox
status: active
owner: engenio
updated: 2026-11-10
source: https://github.com/juancarlord/Wapso/commit/9c4c328514186855939c38935ab1ead438fcafaf
---

# Decisions

Juan locks below were supplied for this dump and checked against git on 2026-11-11. See [[Features]] and [[README]]. There is no `docs/adr/` directory in either repo. The written decisions are these locks plus the acceptance text in [Wapso PR #1](https://github.com/juancarlord/Wapso/pull/1) and `CUTOVER.md` on `kapso-exit`.

## LOCK 2026-10-30 — storage: export about 28 GB to a GCP bucket, then pull into Mundo

Stated lock: export ~28 GB to a GCP bucket, then pull into Mundo (`whatsapp-cloud-inbox`). Avoid a full VPS dump if possible.

What the code on `kapso-exit` @ `9c4c328` actually says (`CUTOVER.md`):

- History and media come from the Kapso API via `npm run kapso:export` (idempotent, resumable). That is an API export, not a VPS disk image. This part matches "no full VPS dump if avoidable".
- Imported media is written to `MEDIA_DIR` on the app disk first. The same file says "about 28 GB / 40,660 files at first". That size is a figure in the runbook. It was not measured in this session.
- A nightly task (`npm run media:tier`, `src/lib/media-tier.ts`) then moves files older than `MEDIA_HOT_DAYS` (default 7) to a private GCS bucket (S3-compatible HMAC, env names `MEDIA_BUCKET_URL`, `MEDIA_BUCKET_ACCESS_KEY_ID`, `MEDIA_BUCKET_SECRET_ACCESS_KEY`). The browser never receives a bucket URL.
- Backups are a separate bucket (`BACKUP_TARGET_URL` and the other `BACKUP_*` names) via `scripts/backup.sh`. `CUTOVER.md` says the backup bucket is not the PACS data bucket, and the media bucket is not the backup bucket.

The implemented order is Kapso API → local disk → private bucket for old media. It is not "put 28 GB in a bucket first, then pull the bucket into Mundo". Whether the buckets have been created is `UNVERIFIED` (the runbook is instructions to Juan, not a completion log).

## LOCK 2026-10-30 — office hours

Stated lock: Monday–Friday 07:00–17:00 and Saturday 07:00–12:00 America/Bogota. Outside that, an after-hours auto-reply and a Fuera de turno queue.

Verified in `src/lib/shift.ts` at `9c4c328` (`kapso-exit` only):

- `DEFAULT_SHIFT`: `timezone: 'America/Bogota'`, `open: '07:00'`, `close: '17:00'`, `days: [1, 2, 3, 4, 5, 6]`, `dayClose: { '6': '12:00' }`, `enabled: true`, `lang: 'es'`.
- Close is exclusive (`17:00:00` is closed). Saturday uses the day override, so Saturday closes at 12:00.
- Sunday is closed (not in `days`).
- `tests/shift.test.ts` names this AC-KC1-1. Those tests passed on 2026-11-11 (16/16 in that file, 45/45 in `tests/*.test.ts`).

The hours delta has landed on `kapso-exit`. It arrived as the squash of PR #1. The feature-branch commit that introduced Saturday midday is `84624c5` (2026-10-30 19:42 America/Bogota), message "AC-KC1-1 hours lock update — Mon–Fri 07:00–17:00 + Sat 07:00–12:00 (midday) COT". That commit is not an ancestor of `9c4c328` (squash, one parent `610fb4c`), but the squash message and the file at the tip both include Saturday 07:00–12:00.

Not landed on Wapso `main` @ `e84601c`. Not present in WhatsappCloudInbox `3db5660`.

Auto-reply and queue marking run only for `INBOX_SOURCE=local`. One reply per contact per closed period. The reply does not remove the chat from Fuera. Agent send or Tomar does. Details in [[Features]].

## LOCK 2026-10-30 — P-009-KC KC1 stays on `kapso-exit`

Stated lock: KC1 on the `kapso-exit` branch only (`9c4c328`). No Coolify, no `main`, no Testini until Juan soft-GOs the next wave.

Verified:

- `9c4c328514186855939c38935ab1ead438fcafaf` exists. Author date 2026-10-30 19:48:12 -0500. Message: "P-009-KC KC1: out-of-shift auto-reply, Fuera queue, Horario (+ Sat 7–12)".
- `git branch --contains 9c4c328` on `juancarlord/Wapso` prints only `kapso-exit`.
- Wapso `main` does not contain that commit. Merge-base of `main` and `kapso-exit` is `a88bd69`.
- WhatsappCloudInbox has no `kapso-exit` branch.

Not verified from code:

- Coolify's deployed branch. No Coolify compose file, no GitHub Actions. Changelog on `main` says the 2026-11-09 work shipped and records no deploy run id.
- The word Testini. No match in either repository. `UNVERIFIED`.
- A soft-GO for the next wave. PR #1 body says "Do not start KC2 until Engenio Soft-GO". No later commit on `kapso-exit` after `9c4c328`. Whether a soft-GO happened outside git is `UNVERIFIED`.

## ADR — squash KC1 into `kapso-exit` (2026-10-30)

[PR #1](https://github.com/juancarlord/Wapso/pull/1) from `feature/p009-kc-cutover` into `kapso-exit`, merged 2026-10-30 19:48 America/Bogota. Four commits on the feature branch, one squash commit on `kapso-exit`. The PR body records Edna PASS WITH NOTES, Securitas CLEAR, Codio READY, and HOLD MERGE until the hours delta. The merge commit includes the hours delta, so the hold was lifted at merge time. Who clicked merge is the GitHub merge commit author `juancarlord`.

## Open — Codio MED-1 before KC3

PR #1 body: "MED-1 server Fuera query parked until before KC3".

Code at `9c4c328`: the queue flag is written in SQLite, and `listLocalConversations` returns it, but the SQL is `ORDER BY last_message_at DESC LIMIT ?` with no off-shift predicate (`src/lib/inbox-read.ts`). The tab filter is in `src/components/conversation-list.tsx`. A server query that returns the Fuera queue (oldest `off_shift_since` first, not limited to the latest 100 chats by activity) is not in this tip.

KC3 in `scripts/kapso-export.mts` is the `--cutover` gap fill (flag imported messages onto the queue, do not auto-reply). It is not MED-1.

Status: open. Not done as of `kapso-exit` `9c4c328` and not present on `main`.
