# MatchCore — Agent Instructions

**MatchCore** is a solo, two-semester graduation project (Istanbul Medeniyet University, Computer Engineering, BIL 497/498, 2026–2027): a **low-latency, deterministic order matching engine**, the core of a stock exchange. It comes with benchmarks, crash recovery through an event log + replay, and a read-only dashboard.

## Always apply

@docs/rules/collaboration.md
@docs/rules/academic.md
@docs/rules/git-and-recap.md

## Where we are now

@docs/memory/project-state.md

## Where things live

| What | Where |
| --- | --- |
| The project's story, one document per step | `docs/steps/NN-name.md` (+ `interactive/` HTML versions) |
| Step list, target dates, status | `docs/steps/README.md` |
| What the system is · version roadmap | `docs/application/README.md` · `docs/application/ROADMAP.md` |
| Design & status per component | `docs/application/{engine,server,client}/` |
| Research material & references | `docs/research/` |
| Decision log | `docs/memory/decisions.md` |
| University rules, deadlines, templates, submissions | `docs/graduation-project/` |
| Code (not started; READMEs hold build/run only) | `src/engine` (C++), `src/server` (Go), `src/client` (web) |

## Conventions

- Documentation in **English**. Official submissions use the Turkish templates.
- Dates in ISO `YYYY-MM-DD`.
- **One question → one home.** Every fact lives in exactly one file; other places link to it.
