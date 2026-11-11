---
type: faq
project: Wapso-Whatsapp-Inbox
status: active
owner: engenio
updated: 2026-11-10
source: https://github.com/juancarlord/WhatsappCloudInbox/commit/3db5660a53fe90ef693cf4995bdf648bd4187604
---

# FAQ

See [[README]], [[Decisions]], [[Commands]].

### Which repo is the inbox?

The checkout dumped here is `juancarlord/WhatsappCloudInbox` at `3db5660` (2025-11-14). The Mundo work since 2026-07 is `juancarlord/Wapso`. They do not share a commit. Both use npm name `whatsapp-cloud-inbox`.

### Which branch should I build?

For this checkout, `main` is the only branch.

For Wapso, use `main` (`e84601c`) for the Kapso inbox the changelog calls shipped on 2026-11-09. Use `kapso-exit` (`9c4c328`) for the Meta-direct cutover and Fuera de turno. Do not expect one branch to contain both. They diverged at `a88bd69` on 2026-10-19.

### Where are office hours implemented?

`src/lib/shift.ts` on `kapso-exit` at `9c4c328`, and the same defaults on `feature/p009-kc-cutover` at `84624c5`. Not on Wapso `main`. Not in the WhatsappCloudInbox checkout. The hours in code match the 2026-10-30 lock, including Saturday 07:00–12:00 America/Bogota. See [[Features]].

### Does the 24-hour WhatsApp window and the office-hours window do the same thing?

No. The 24-hour window is Meta's customer-care rule (free-form vs template). In the checkout it is a UI check only. On `kapso-exit` local mode, send returns 409 `window_closed` outside 24 hours (`CUTOVER.md`, `WINDOW_MS` in `src/lib/outbound.ts`). Office hours are a separate Mundo schedule that queues the chat and sends one auto-reply per closed period.

### Is KC1 on main or on Coolify?

`9c4c328` is only on `kapso-exit`. Wapso `main` has no shift module. No Coolify file is in either repo, and the changelog records no deploy run id, so the live Coolify git SHA is `UNVERIFIED`. The changelog text says the 2026-11-09 `main` commits shipped. Nothing in git says `kapso-exit` was deployed.

### What is Testini?

No match in either repo (code, docs, branch names). `UNVERIFIED`.

### What is still open before KC3?

[Wapso PR #1](https://github.com/juancarlord/Wapso/pull/1) says "MED-1 server Fuera query parked until before KC3". The string `MED-1` is not in the source. The code still filters Fuera in the browser over a 100-row list. `scripts/kapso-export.mts` has a `--cutover` flag commented as "KC3 gap fill": imported inbound messages can be flagged onto the Fuera queue and are not auto-replied. That flag is not the server-side queue query.

### Where do env values live?

Names only in git (`.env.example` on Wapso, `README.md` on the checkout). `CUTOVER.md` says production values are in Coolify. Do not copy values into notes. After the first admin login, Wapso's README says to remove `ADMIN_PASSWORD_HASH` and `ADMIN_USERNAME` from Coolify so a later boot does not restore that password.

### How do I run tests?

The checkout has no test script. On `kapso-exit`, `npm test` is `node --test --experimental-strip-types --no-warnings tests/*.test.ts`. 45 passed on 2026-11-11 at `9c4c328`. Wapso `main` has `npm run test:media-text` only. That script was not run.

### Why does the Wapso README say there is no login?

The top of `README.md` on `main` was not updated when auth landed (`c6edafd`, 2026-10-19). The code has login. See [[Quirks]].
