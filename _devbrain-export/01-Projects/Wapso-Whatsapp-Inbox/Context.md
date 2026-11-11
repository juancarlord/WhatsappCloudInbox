---
type: context
project: Wapso-Whatsapp-Inbox
status: active
owner: engenio
updated: 2026-11-10
source: https://github.com/juancarlord/Wapso/blob/e84601c/docs/SUITE-WAVE0-LEARNINGS-FOR-MUNDO.md
---

# Context

See [[README]] and [[Decisions]].

## What this product is

A single-business-number WhatsApp inbox for Mundo Radiológico, built from Kapso's open-source inbox template (`README.md` on Wapso `main`; the checkout README still points at `gokapso/whatsapp-cloud-inbox`). Wapso `CHANGELOG.md` at `e84601c` says customer-facing wording should call it the Tekobo Suite business messaging integration, with Mundo as the licensee, and should not say "Wapso".

The checkout (`3db5660`, 2025-11-14) is that template uploaded as one commit: poll Kapso, send text, media, templates, and buttons. Wapso `main` (first commit `85a511b`, 2026-07-06 11:36 America/Bogota, "new interface") is a later adaptation: auth, roles, assignment, metrics, and on 2026-11-09 a full history search. `kapso-exit` is the in-progress replacement of Kapso by a Meta webhook and a local SQLite store (P-009).

## Where it runs

Wapso `README.md` on `main` says the app is on the Mundo Radiológico intranet via Coolify (Nixpacks): build `npm run build`, start `npm start`, port 3000, Node `>=22.5`, a volume at `/app/data` with `DATA_DIR=/app/data`. The documented `APP_ORIGIN` example in that README is `https://whatsapp-mundoradiologico.tekobo.io`. No Coolify definition is committed. Whether that host is serving `e84601c` is `UNVERIFIED` beyond the changelog sentence.

`CUTOVER.md` on `kapso-exit` says nothing is live on the local-inbox path until `INBOX_SOURCE=local`. The webhook callback path in that runbook is `/api/webhooks/meta` on the same host. Subscribing that callback was not confirmed from this session.

## Cutover learnings doc

`docs/SUITE-WAVE0-LEARNINGS-FOR-MUNDO.md` on Wapso `main` at `41708e6` (2026-10-30 22:09 America/Bogota). It is ops notes from Tekobo Suite Wave 0 (Meta direct, not Kapso) for the Mundo cutover. It contains Meta phone-number ids, a WABA id, and a phone number. Those values are not copied into this vault.

Lessons that are safe to keep as words:

- A green webhook verify is not enough. The number also needs webhook subscription turned on, or inbound messages never arrive and no error is shown.
- Coolify interpolates `$`. `META_WEBHOOK_VERIFY_TOKEN` and `ADMIN_PASSWORD_HASH` need to be stored as literal values, or the dollar signs doubled, and checked after save.
- The doc says the Resend alert env names are required for the cutover process to boot: `ALERT_EMAIL_TO`, `ALERT_EMAIL_FROM`, `ALERT_RESEND_API_KEY`.
- `PHONE_NUMBER_ID` must be the Mundo number's id. Messages whose metadata phone-number id does not match are dropped. The doc warns not to mix the Suite WABA with the Mundo WABA.
- `APP_ORIGIN` must match exactly and must not have a trailing slash, or login returns 403 "origin".
- `POST /{WABA_ID}/subscribed_apps` may return that the API is not supported on newer Meta messaging accounts. The doc says to confirm subscription in the Meta UI in that case. Whether that applies to Mundo is left as a TODO in the doc. `UNVERIFIED` here as well.

## People and roles named in git

Commit author on both remotes is juancarlord. PR #1 and `src/lib/shift.ts` mention Edna for the after-hours copy lock ("PASS WITH NOTES"). The changelog mentions Rocky relaying Juan's ship confirmation on 2026-11-09. Codio is named in the PR body for the MED-1 queue query and for `safeError` on templates and media. No other identity is established by the code.

## What not to put in notes

Passwords, token values, API keys, webhook verify tokens, HMAC secrets, phone numbers, and the Meta ids from the learnings doc. Env var names only. The hard-coded canned reply in Wapso `src/components/message-view.tsx` contains a PBX number; the number is omitted here. See [[Quirks]].
