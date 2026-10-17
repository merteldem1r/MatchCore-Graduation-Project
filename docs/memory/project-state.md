# Project State

> **Where we are right now, nothing else.** Rewrite this file at the end of each session; never append history. Keep it under ~40 lines. History lives in `docs/steps/`, `decisions.md` and git; component details live in `docs/application/<component>/README.md`.

**Last updated:** 2026-10-17

## Current step

**04 · Why this is a computer engineering problem**: next to start (target 2026-10-19).

## Recently done

- 03 Name & repository structure: codename **MatchCore**, "one question → one home" docs layout, AI context (rules + state auto-loaded, `/step` and `/wrap-up` skills), git rule (2026-10-17).
- Repo tooling: Conventional Commits enforced by `.githooks/commit-msg` + a GitHub Actions check; Claude is blocked from `git add` / `commit` / `push` (2026-10-17).
- Root README rewritten as a high-level overview (components, documents, structure); MIT license added; no-emoji rule (2026-10-17).

## Components

Engine, server, client: not started. See `docs/application/README.md`.

## Next actions

1. Step 04: latency vs. throughput, percentiles, what makes code fast/slow, data structures per operation, determinism.
2. Keep the pace in `docs/steps/README.md`; the proposal must be sent by **2026-11-01**.
3. On any new clone or machine: `git config core.hooksPath .githooks`.

## Open questions

| Question | Needed for | Status |
| --- | --- | --- |
| Advisor's full title and name | Proposal form | Waiting on student |
| Is the interim report required? | Fall planning | Unknown; assume yes |
| Spring 2027 calendar (thesis deadline) | Spring planning | Not published yet |
| Replace leftover emojis in older docs (steps README, steps 02 and 03)? | Consistency with the no-emoji rule | Asked, not answered |

## Context

Solo project. The student is a web developer with Go and C++ experience, new to the market domain, so explain from first principles. Tentative stack: C++ engine, Go + web dashboard.
