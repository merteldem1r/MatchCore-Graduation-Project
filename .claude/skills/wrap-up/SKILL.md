---
name: wrap-up
description: End-of-session bookkeeping for MatchCore. Updates project state, the decision log and statuses, then gives a recap with suggested git commands. Use when the student says "wrap up", "let's finish for today", or /wrap-up, or when a step is finished.
---

# Wrap-up

1. `docs/memory/project-state.md`: **rewrite** it to reflect now (Last updated, Current step, Recently done, Components, Next actions, Open questions). Don't append history; keep it under ~40 lines.
2. `docs/memory/decisions.md`: append any new decisions (next `D` number, date, area, why, alternatives, step link).
3. Update statuses: `docs/steps/README.md` (steps) and, if a component changed, `docs/application/<component>/README.md`.
4. Finish with the recap and the suggested `git add` / `git commit` commands from `docs/rules/git-and-recap.md`. **Never run git add, commit or push yourself.**
