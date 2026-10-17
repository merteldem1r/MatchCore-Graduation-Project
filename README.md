# Order Matching Engine — Graduation Project

> *Working title; the codename is still being chosen (see [step 03](docs/steps/03-name-and-repo-structure.md)).*

A **low-latency, deterministic order matching engine**: the program at the core of a stock exchange that pairs buy and sell orders by **price-time priority**. The project uses it to compare order-book data structures, measure latency and throughput, and recover exactly from crashes through an event log and deterministic replay. A read-only web dashboard visualizes the order book and latency metrics.

Graduation project · Computer Engineering · Istanbul Medeniyet University · 2026–2027 (BIL 497 / BIL 498)

## Status

🟡 **Early stage: understanding the problem and planning.** No code yet.
Current step and next actions: [`docs/memory/project-state.md`](docs/memory/project-state.md)

## Planned components

| Component | Tech (tentative) | Folder |
| --- | --- | --- |
| Matching engine (order book, matching, event log, replay) | C++ | [`src/engine`](src/engine) |
| Metrics / dashboard backend | Go | [`src/server`](src/server) |
| Dashboard UI (read-only) | Web | [`src/client`](src/client) |

## Documentation

- [**Steps**](docs/steps/): the project explained step by step, from the idea onward
- [Plan](docs/application/PLAN.md) · [System overview](docs/application/README.md) · [Resources](docs/application/resources.md)
- [Graduation project rules, deadlines & templates](docs/graduation-project/README.md)
- [All documentation](docs/README.md)
