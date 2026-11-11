---
type: change
project: Wapso-Whatsapp-Inbox
status: recorded
owner: engenio
updated: 2026-11-10
source: https://github.com/juancarlord/Wapso/commit/41708e6
---

# 2026-10-30 `41708e6` Suite Wave 0 learnings

What: Adds an ops note for the Mundo cutover, taken from Tekobo Suite Wave 0 (Meta direct, not Kapso). Why: commit subject. Docs only. On Wapso `main`. Not on `kapso-exit` (the branch split is `a88bd69`, and this commit is after it).

Link: https://github.com/juancarlord/Wapso/commit/41708e6

Author date: 2026-10-30 22:09 America/Bogota. No pull request.

`git diff --stat`: 2 files, +24. `docs/SUITE-WAVE0-LEARNINGS-FOR-MUNDO.md` (new, 21 lines) and a `CHANGELOG.md` line.

- The note says a green verify is not the same as webhook subscription being on.
- It warns that Coolify eats `$` in env values, so token and hash vars must be literal.
- It says `PHONE_NUMBER_ID` must be Mundo's id and that Suite and Mundo WABAs must not be mixed.
- The file includes numeric Meta ids and a phone number. Those values are not repeated in this note. See [[Context]].

Risks: the ids in that doc are production identifiers sitting in a public GitHub file. This dump does not remove them (docs-only task on the inbox repo was not to edit Wapso). Treat the file as sensitive even though it is public.

How tested: not applicable (markdown). The checklist was not executed against Meta.
