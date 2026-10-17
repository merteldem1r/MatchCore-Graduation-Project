# Agent Instructions

This repository is a solo, two-semester **graduation project** (Istanbul Medeniyet University, Computer Engineering, BIL 497/498, 2026–2027): a **low-latency, deterministic order matching engine**, the core of a stock exchange, plus benchmarks, crash recovery and a read-only dashboard.

## Read first, every session

1. [`docs/memory/project-state.md`](docs/memory/project-state.md): where we are, what's next, open questions.
2. [`docs/rules/collaboration.md`](docs/rules/collaboration.md): how we work. **Most important rule: step by step, simplest version first, explain before building.**
3. [`docs/rules/academic.md`](docs/rules/academic.md): academic integrity and data rules.
4. The latest document in [`docs/steps/`](docs/steps/).

## Where things live

| What | Where |
| --- | --- |
| The project's story, one document per step | `docs/steps/NN-name.md` (+ `interactive/` HTML versions) |
| Step plan, target dates, status | `docs/application/PLAN.md` |
| What the system is (components, scope, versions) | `docs/application/README.md` |
| Research material, similar projects, references | `docs/application/resources.md` |
| Current state / decision log | `docs/memory/project-state.md` / `docs/memory/decisions.md` |
| University rules, deadlines, templates, our submissions | `docs/graduation-project/` |
| Code (not started yet) | `src/engine` (C++), `src/server` (Go), `src/client` (web) |

## Key deadlines

- **2026-11-01**: proposal form (*proje başvuru formu*) emailed to the department. See `docs/graduation-project/README.md`.
- **~2026-12-14 to 12-20 (fall week 14)**: interim report, if the advisor requests it.
- **Spring 2027, end of week 13**: thesis. Then the jury presentation.

## Conventions

- Documentation is in **English**. Official submissions use the Turkish templates.
- Dates are ISO `YYYY-MM-DD`.
- When a step finishes, update `docs/memory/project-state.md`, add decisions to `docs/memory/decisions.md`, and update the status in `docs/application/PLAN.md` and `docs/steps/README.md`.
- Do not commit personal data (student number, private emails) or anything from a previous employer.
- Only commit or push when the user asks.
