# How We Work

Rules for everyone working on this repository, human or AI agent.

## Pace and scope

1. **Step by step.** One step at a time, in the order of [`../application/PLAN.md`](../application/PLAN.md). Finish, document and check in before starting the next.
2. **Simplest version first.** Build the simplest *correct* version, then improve it in measured upgrades (v1 → v2 → …). Advanced options are presented as later upgrades, not as the starting point.
3. **Understand before building.** Explain concepts from first principles, with small worked examples. No code is written until the design step for it is done and the student asks for it.
4. **No one-shot generation.** Never scaffold or generate large parts of the project at once.

## Documentation

5. Every step ends with a document in [`../steps/`](../steps/) (skeleton in `steps/README.md`).
6. After each session, update [`../memory/project-state.md`](../memory/project-state.md). Log decisions in [`../memory/decisions.md`](../memory/decisions.md).
7. Long explanations can also get an interactive HTML version in `steps/interactive/`; the Markdown version is the main one.

## Git

8. Commit or push only when the student asks. Write clear commit messages.

## Code (once it starts)

9. The engine's critical path stays simple, deterministic and measurable. Anything that is not matching (logging to the dashboard, metrics export) must not slow it down.
10. Every performance claim needs a benchmark that backs it up.
