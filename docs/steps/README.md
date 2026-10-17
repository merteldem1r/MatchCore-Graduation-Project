# Steps

This folder is the project's story, told one step at a time. Each numbered document covers one step: what we learned, what we decided, and what comes next. Read them in order to see how the project grew from an idea into a system.

| # | Step | Status | Interactive version |
| --- | --- | --- | --- |
| 01 | [Project idea](01-project-idea.md) | Done (2026-10-16) | [kickoff page](interactive/01-project-kickoff.html) |
| 02 | [How an exchange works](02-how-an-exchange-works.md) | Done (2026-10-17) | [with order-book simulator](interactive/02-how-an-exchange-works.html) |
| 03 | [Project name & repository structure](03-name-and-repo-structure.md) | In progress | — |
| 04 | Why this is a computer engineering problem | Next | — |
| 05 | Similar projects & research material | Planned | — |
| 06 | Tech stack | Planned | — |
| 07 | Scope, architecture & v1 design | Planned | — |
| 08 | Proposal form (final title + two-semester timeline) | Planned (must be sent by 2026-11-01) | — |

The full plan with target dates is in [`../application/PLAN.md`](../application/PLAN.md).

## How a step document is written

- File name: `NN-short-kebab-name.md`, numbered in the order the steps happen. Numbers are never reused.
- Language: English. The official university submissions are written in Turkish separately (see `../graduation-project/submissions/`).
- Dates: ISO format, `YYYY-MM-DD`.
- Interactive HTML versions, if a step has one, go in `interactive/` under the same number.

Every step document uses this skeleton:

```markdown
# NN · Title

> **Status:** done | in progress · **Date:** YYYY-MM-DD

## Goal of this step
## Content            ← the explanation / analysis itself
## Decisions          ← what we decided (also logged in docs/memory/decisions.md)
## Open questions
## Next step
```

## Why keep these documents

1. **Understanding.** Each step is written so the topic can be explained in plain words, which is exactly what the jury will ask for.
2. **The thesis.** The *Genel Kısımlar*, *Materyal ve Yöntem* and *Tartışma* chapters can be built from these notes. The thesis text itself must be rewritten in our own words, with citations (it is checked with Turnitin).
3. **Continuity.** Any session, human or AI, can pick up where the last one stopped.
