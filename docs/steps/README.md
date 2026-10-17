# Steps

This folder is the project's story, told one step at a time. Each numbered document covers one step: what we learned, what we decided, and what comes next. Read them in order to see how the project grew from an idea into a system.

## What a step is (and isn't)

Steps are **high-level and project-wide**: the milestones in *understanding* the problem and the *big decisions* we make together. They are never about a single component or feature.

| Belongs in a step | Does not belong in a step → where it goes instead |
| --- | --- |
| Understanding a new topic (how exchanges work, what latency means) | Design of a component or version → `docs/application/<component>/` |
| A project-wide decision (name, tech stack, scope, how we measure) | Feature progress, bugs, small changes → component README + git history |
| A turning point (proposal sent, first version working, interim report) | Build / run instructions → `src/<component>/README.md` |
| A short summary + link when a step leads to a design | The full design text (written once, in `application/`) |

**Test before creating a new step:** *Will this still matter when explaining the project's story to the jury?* If not, it isn't a step.

## Step list

**This table is the single place where step status is tracked.** Target dates are set so the proposal is sent by **2026-11-01**, with a few days' buffer for advisor feedback.

| # | Step | Target | Status | Interactive version |
| --- | --- | --- | --- | --- |
| 01 | [Project idea](01-project-idea.md) | 2026-10-16 | Done | [kickoff page](interactive/01-project-kickoff.html) |
| 02 | [How an exchange works](02-how-an-exchange-works.md) | 2026-10-17 | Done | [with order-book simulator](interactive/02-how-an-exchange-works.html) |
| 03 | [Project name & repository structure](03-name-and-repo-structure.md) | 2026-10-17 | Done | — |
| 04 | Why this is a computer engineering problem | 2026-10-19 | Next | — |
| 05 | Similar projects & research material | 2026-10-21 | Planned | — |
| 06 | Tech stack | 2026-10-22 | Planned | — |
| 07 | Scope & architecture (what v1 is; its detailed design goes to `application/engine/`) | 2026-10-24 | Planned | — |
| 08 | Proposal form: final title, two-semester timeline | draft 10-27 · send by 10-30 | Planned | — |

Midterm week: 2026-11-02 → 11-08. Steps after 08 are added here as they are planned. The product's version plan is in [`../application/ROADMAP.md`](../application/ROADMAP.md).

## How a step document is written

- File name: `NN-short-kebab-name.md`, numbered in the order the steps happen. Numbers are never reused.
- Language: English. The official university submissions are written in Turkish separately (see `../graduation-project/submissions/`).
- Dates: ISO format, `YYYY-MM-DD`.
- Interactive HTML versions, if a step has one, go in `interactive/` under the same number.
- A step records the **discussion and decisions**. If it produces a design (e.g. the v1 engine design), the design itself goes in `docs/application/<component>/`, and the step document links to it.

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
