---
type: change
project: Wapso-Whatsapp-Inbox
status: recorded
owner: engenio
updated: 2026-11-10
source: https://github.com/juancarlord/Wapso/commit/c6edafd
---

# 2026-10-19 `c6edafd` Login and user admin

What: Adds login, sessions, and team-lead user admin on SQLite. Why: the commit message is "feat(auth): login, sessions, and team-lead user admin (SQLite)". This is on Wapso `main` and is an ancestor of `kapso-exit`. It is not in WhatsappCloudInbox.

Link: https://github.com/juancarlord/Wapso/commit/c6edafd

Author date: 2026-10-19 16:34 America/Bogota. No pull request.

`git diff --stat` vs parent: 31 files, +1019 / −56. Main paths: `src/middleware.ts`, `src/lib/auth.ts`, `src/lib/session.ts`, `src/lib/db.ts`, `src/lib/password.ts`, `src/lib/rate-limit.ts`, `src/app/login/page.tsx`, `src/app/admin/usuarios/page.tsx`, `src/app/api/auth/*`, `src/app/api/admin/users/*`, `.env.example`, `scripts/hash-password.mjs`, `README.md`.

- Middleware requires a session for pages and `/api/*`. Public paths are `/login` and `/api/auth/login`.
- Users live in `$DATA_DIR/inbox.db` via `node:sqlite`. Roles in this commit start the admin/lead/agent model (lead migration is in this file's later history; the table CHECK includes `lead` by `e84601c`).
- Session cookie is httpOnly. State-changing API calls check the origin.
- Login is rate limited in memory.
- Bootstrap admin uses `ADMIN_USERNAME` and `ADMIN_PASSWORD_HASH`. The hash script reads a hidden input.
- `package.json` gains `hash-password` and `engines.node` `>=22.5`.

Risks: `AUTH_SECRET` shorter than 32 characters makes every request 503. Leaving `ADMIN_PASSWORD_HASH` in the host restores that admin when no active admin exists. The rate limiter is per process.

How tested: not run for this commit. Later `kapso-exit` tests do not cover the login form. See [[Features]].
