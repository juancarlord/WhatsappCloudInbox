---
type: commands
project: Wapso-Whatsapp-Inbox
status: active
owner: engenio
updated: 2026-11-10
source: https://github.com/juancarlord/WhatsappCloudInbox/blob/3db5660a53fe90ef693cf4995bdf648bd4187604/package.json
---

# Commands

Checked against `package.json` and the absence of `.github/workflows` on 2026-11-11. No GitHub Actions file exists in WhatsappCloudInbox `3db5660`, Wapso `main` `e84601c`, or Wapso `kapso-exit` `9c4c328`. External CI (Coolify build logs) was not opened. `UNVERIFIED` whether Coolify runs these scripts as written.

`npm install`, `npm run dev`, `npm run build`, and `npm run lint` were not executed in the checkout. The Kapso client needs env vars, and this dump does not put values in the environment.

## Checkout `3db5660` (`package.json`)

| Script | Command | Notes |
|---|---|---|
| `npm run dev` | `next dev --turbopack -p 4000` | README says open `http://localhost:4000`. |
| `npm run build` | `next build` | Not run here. |
| `npm start` | `next start` | Next default port 3000, not 4000. |
| `npm run lint` | `eslint` | Not run here. |

No `test` script. No `engines` field. Dependencies include `next` `15.5.4`, `react` `19.1.0`, `@kapso/whatsapp-cloud-api` `^0.0.6`.

Env names required by the client and the templates route: `PHONE_NUMBER_ID`, `KAPSO_API_KEY`, `WABA_ID`.

## Wapso `main` @ `e84601c`

Same `dev` / `build` / `start` / `lint` scripts. Also:

| Script | Command |
|---|---|
| `npm run hash-password` | `node scripts/hash-password.mjs` |
| `npm run test:media-text` | `node --experimental-strip-types --no-warnings scripts/test-media-text.mts` |

`engines.node` is `>=22.5` (SQLite `node:sqlite`). This machine's Node during the dump was v22.23.4. `test:media-text` was not run. README Coolify settings: build `npm run build`, start `npm start`, port 3000.

## Wapso `kapso-exit` @ `9c4c328`

All of the `main` scripts, plus:

| Script | Command |
|---|---|
| `npm test` | `node --test --experimental-strip-types --no-warnings tests/*.test.ts` |
| `npm run kapso:export` | `node --experimental-strip-types --no-warnings scripts/kapso-export.mts` |
| `npm run media:tier` | `node --experimental-strip-types --no-warnings scripts/media-tier.mts` |

`npm test` was run on 2026-11-11 in a detached worktree of `9c4c328` (no `npm install`; the tests import local TypeScript and `node:sqlite` only). Result: 45 passed, 0 failed. That includes `tests/shift.test.ts`, `tests/webhook.test.ts`, `tests/media-tier.test.ts`, and `tests/lib.test.ts`.

`kapso:export` and `media:tier` were not run. They call Kapso or a bucket. `CUTOVER.md` says the export is resumable and that `--dry-run` exists. Flags were not executed.

`scripts/backup.sh` is invoked by the runbook as a Coolify scheduled task (`bash scripts/backup.sh`), not as an npm script. Not run.
