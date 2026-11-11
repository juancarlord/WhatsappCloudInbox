---
type: project
project: Wapso-Whatsapp-Inbox
status: active
owner: engenio
updated: 2026-11-10
source: https://github.com/juancarlord/WhatsappCloudInbox/commit/3db5660a53fe90ef693cf4995bdf648bd4187604
---

# Wapso / WhatsApp Cloud Inbox

Next.js inbox for a WhatsApp Business number. Package name in both git remotes below is `whatsapp-cloud-inbox`. DevBrain folder name is `Wapso-Whatsapp-Inbox`.

These notes follow the dump rules given for this job (frontmatter, wikilinks, America/Bogota dates, env var names only). The DevBrain vault clone failed (`https://github.com/juancarlord/DevBrain` returned "repository not found" on 2026-11-11), so `00-Meta/CONVENTIONS.md`, `DUMP-PROTOCOL.md`, and `00-Meta/Templates/*` were not read. Anything those files require beyond the stated rules is `UNVERIFIED`.

## Two remotes

| Remote | What it is | Tip | Working branch |
|---|---|---|---|
| [juancarlord/WhatsappCloudInbox](https://github.com/juancarlord/WhatsappCloudInbox) | The checkout this dump started from. One commit, 2025-11-14 11:54 America/Bogota. Kapso-template inbox. No login, no database, no office hours. | `3db5660` | `main` is the only branch |
| [juancarlord/Wapso](https://github.com/juancarlord/Wapso) | The continued Mundo Radiológico inbox. Same package name. Not a git descendant of `3db5660` (no shared commit). | see branches | two lines, see below |

`juancarlord/Wapso` is clearly the same product (package name, Kapso client, Mundo copy, path `whatsapp-cloud-inbox` in its own `CUTOVER.md`). It is not trivially small (97 files on `main`, 121 on `kapso-exit`). This dump does not copy that tree. It records the relationship and the facts the task asked to verify (branches, office hours, KC1, storage lock, recent commits). Paths below that are not in the WhatsappCloudInbox checkout are marked with the Wapso SHA.

Local path `~/Dev/whatsapp-cloud-inbox` was not on this machine. `UNVERIFIED` which directory Juan's laptop currently has checked out.

## Wapso branches (verified 2026-11-11)

`git branch -a` on `juancarlord/Wapso`:

- `main` at `e84601c` (2026-11-09 10:33 America/Bogota). Changelog in that commit says the 2026-11-09 inbox changes shipped. This line still talks to Kapso. It does not contain `src/lib/shift.ts`.
- `kapso-exit` at `9c4c328` (2026-10-30 19:48 America/Bogota). Meta webhook, local SQLite store, Kapso export, and KC1 (Fuera de turno). `git branch --contains 9c4c328` returns only `kapso-exit`.
- `feature/p009-kc-cutover` at `84624c5` (2026-10-30 19:42 America/Bogota). Pull request head. Still on the remote after the merge.

The lines split at `a88bd69` (2026-10-19 21:58 America/Bogota). After that, `main` gained the 2026-11-09 history/search commits. `kapso-exit` gained `610fb4c` and `9c4c328`. Neither contains the other's later commits.

Working branch, stated as two lines because the repo has two:

- Shipped Kapso inbox: Wapso `main`. Evidence is `CHANGELOG.md` at `e84601c` ("Shipped, per Juan's confirmation relayed by Rocky (2026-11-09 ~10:29 America/Bogota); no deploy run id recorded"). Which Coolify ref is actually running is `UNVERIFIED` (no deploy run id, no Coolify config in git).
- Kapso-exit / KC1 work: `kapso-exit`. That is the branch the 2026-10-30 lock names. It is not merged to Wapso `main`.

This checkout's only branch is `main`. It is not the Kapso-exit line and it is not the 2026-11-09 shipped line.

Pull request: [Wapso #1](https://github.com/juancarlord/Wapso/pull/1), merged 2026-10-30 19:48 America/Bogota into `kapso-exit`. GitHub merge commit is `9c4c328`, one parent `610fb4c` (squash). The four commits on `feature/p009-kc-cutover` (`e6f0fc5`, `8bbaa31`, `1ee11e3`, `84624c5`) are not contained in `kapso-exit`; their content is in the squash.

`WhatsappCloudInbox` has no pull requests and no tags.

## Notes

- [[Codebase-Map]]
- [[Features]]
- [[Quirks]]
- [[FAQ]]
- [[Decisions]]
- [[Context]]
- [[Commands]]

Change notes (newest first) live in `05-Changes/Wapso-Whatsapp-Inbox/`:

- [[2026-11-09-6cf6467-changelog]]
- [[2026-11-09-5dafddb-history-search]]
- [[2026-11-09-19c290b-full-history-media]]
- [[2026-10-30-41708e6-suite-learnings]]
- [[2026-10-30-9c4c328-kc1-fuera]]
- [[2026-10-23-610fb4c-meta-direct]]
- [[2026-10-19-a88bd69-metrics]]
- [[2026-10-19-088241e-novedades]]
- [[2026-10-19-64cf527-assignment]]
- [[2026-10-19-fc4da0c-roles-ui]]
- [[2026-10-19-c6edafd-auth]]
- [[2025-11-14-3db5660-files-up]]
