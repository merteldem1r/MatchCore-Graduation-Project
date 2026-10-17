# MatchCore - Order Mathching Engine | Graduation Project

**Author**: Mert Eldemir
</br>
**Student NO**: 25120205086

A **low-latency, deterministic order matching engine**: the program at the core of a stock exchange that pairs buy and sell orders by **price-time priority**.

The project uses the engine to compare order-book data structures, measure latency and throughput, and recover exactly from crashes through an event log and deterministic replay. A read-only web dashboard shows the order book and latency metrics.

Graduation project · Computer Engineering · Istanbul Medeniyet University · 2026–2027 (BIL 497 / BIL 498)

## Status

**Early stage: understanding the problem and planning.** No code yet.

- Where we are now: [`docs/memory/project-state.md`](docs/memory/project-state.md)
- Step list and progress: [`docs/steps/README.md`](docs/steps/README.md)

## Planned components

| Component | Role | Tech (tentative) | Code | Docs |
| --- | --- | --- | --- | --- |
| Engine | Order book, matching, event log, replay | C++ | [`src/engine`](src/engine) | [`docs/application/engine`](docs/application/engine) |
| Server | Reads engine output, serves metrics | Go | [`src/server`](src/server) | [`docs/application/server`](docs/application/server) |
| Client | Read-only web dashboard | Web | [`src/client`](src/client) | [`docs/application/client`](docs/application/client) |

## Documents

| Document | What it is about | Link |
| --- | --- | --- |
| Steps | The project's story: the idea, what we learned and the big decisions, one document per step | [`docs/steps/`](docs/steps/) |
| System overview | What the system is, its components and scope | [`docs/application/README.md`](docs/application/README.md) |
| Roadmap | Planned versions of the software and the main milestones | [`docs/application/ROADMAP.md`](docs/application/ROADMAP.md) |
| Component docs | Design and status of each component (engine, server, client) | [`docs/application/`](docs/application/) |
| Research | Literature, similar projects and data sources | [`docs/research/`](docs/research/) |
| Project state | Where the project is right now and what comes next | [`docs/memory/project-state.md`](docs/memory/project-state.md) |
| Decision log | Every important decision, with the reason and the alternatives | [`docs/memory/decisions.md`](docs/memory/decisions.md) |
| Rules | How we work: collaboration, academic integrity, git | [`docs/rules/`](docs/rules/) |
| Graduation project | University guidelines, deadlines, templates and our submissions | [`docs/graduation-project/`](docs/graduation-project/) |

## Repository structure

```text
.
├── README.md                  Project overview (this file)
├── AGENTS.md                  Instructions for AI agents
├── CLAUDE.md                  Claude Code entry point
├── LICENSE                    MIT license
├── .claude/                   Claude Code configuration
├── .githooks/                 Local git hooks
├── .github/                   GitHub configuration and CI workflows
│
├── docs/
│   ├── steps/                 The project's story: idea, learning and decisions, step by step
│   ├── application/           The software: system overview, roadmap, design per component
│   ├── research/              Literature, similar projects, data sources
│   ├── memory/                Current project state and the decision log
│   ├── rules/                 How we work: collaboration, academic integrity, git
│   └── graduation-project/    University side: guidelines, deadlines, templates, submissions
│
└── src/
    ├── engine/                Matching engine (C++)
    ├── server/                Server (Go)
    └── client/                Web dashboard
```

Each kind of information has exactly one home; other files link to it instead of repeating it.

## Where to start reading

1. [`docs/steps/`](docs/steps/): read the steps in order to follow how the project developed.
2. [`docs/application/README.md`](docs/application/README.md): what the system is and how its parts fit together.
3. [`docs/application/ROADMAP.md`](docs/application/ROADMAP.md): planned versions and milestones.
4. [`docs/graduation-project/README.md`](docs/graduation-project/README.md): university deadlines and required documents.

## Setup

After cloning, enable the commit-message check (once per clone):

```bash
git config core.hooksPath .githooks
```

## License

MatchCore is released under the [MIT License](LICENSE). You may use, copy, modify and distribute it, provided the copyright notice and license text are kept.
