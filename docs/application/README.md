# MatchCore — System Overview

> **What the system is.** This is a living document: it describes the current design. The history of *why* is in [`../steps/`](../steps/); the version plan is in [`ROADMAP.md`](ROADMAP.md).

## What it does

Receives buy/sell orders for an instrument, keeps them in an **order book**, and matches them by **price-time priority**, producing trade events deterministically.

## Components

```text
 order input            ┌────────────────────────┐           ┌──────────────┐     ┌──────────────┐
 (file / generator) ──> │  Matching engine (C++) │ ──events─>│ Server (Go)  │ ──> │ Client (web) │
                        │  order book + matching │           │ metrics, API │     │ dashboard,   │
                        └───────────┬────────────┘           └──────────────┘     │ read-only    │
                                    │ event log + snapshots                       └──────────────┘
                                    v
                              disk (replay after crash)
```

| Component | Role | Design docs | Code | Status |
| --- | --- | --- | --- | --- |
| Engine | Critical path: order book, matching, event log, replay. Single-threaded per book. | [`engine/`](engine/) | `src/engine` | Not started |
| Server | Reads engine output; serves metrics to the dashboard. Off the critical path. | [`server/`](server/) | `src/server` | Not started |
| Client | Read-only web dashboard: depth, trades, latency. | [`client/`](client/) | `src/client` | Not started |

## Scope

| Part | Priority |
| --- | --- |
| Deterministic matching (limit, market, cancel, modify) | Core |
| Data structure comparison | Core (research questions) |
| Benchmarks: throughput, p50/p99/p99.9/max latency, memory, allocations | Core |
| Event log + snapshot + replay | Core |
| Raft replication | Optional, minimal |
| Web dashboard | Extra, read-only |

## Where documentation goes

- `docs/application/<component>/README.md`: what the component is, its current version and status.
- `docs/application/<component>/vN-design.md`: the design of each version, written when that version is designed.
- `docs/application/engine/benchmarks.md`: results across versions (once measuring starts).
- `src/<component>/README.md`: **only** how to build, run and test.
