# MatchCore — System Overview

> What the software is and how it is put together. Updated as the design evolves (step 07 onward). For the history of *why*, see [`../steps/`](../steps/).

## What it does

Receives buy/sell orders for an instrument, keeps them in an **order book**, and matches them by **price-time priority**, producing trade events deterministically.

## Components (planned)

```text
 order input            ┌────────────────────────┐           ┌──────────────┐     ┌──────────────┐
 (file / generator) ──▶ │  Matching engine (C++) │ ──events─▶│ Server (Go)  │ ──▶ │ Dashboard    │
                        │  order book + matching │           │ metrics, API │     │ (web, read-  │
                        └───────────┬────────────┘           └──────────────┘     │  only)       │
                                    │ event log + snapshots                       └──────────────┘
                                    ▼
                              disk (replay after crash)
```

- **Engine**: the critical path. Single-threaded per order book. Must never wait on the dashboard.
- **Event log + snapshots**: inputs recorded in order, so the book can be rebuilt by deterministic replay.
- **Server + dashboard**: separate processes that read the engine's outputs; off the critical path.

## Scope

| Part | Priority |
| --- | --- |
| Deterministic matching (limit, market, cancel, modify) | Core |
| Data structure comparison | Core (research questions) |
| Benchmarks: throughput, p50/p99/p99.9/max latency, memory, allocations | Core |
| Event log + snapshot + replay | Core |
| Raft replication | Optional, minimal, spring |
| Web dashboard | Extra, read-only |

## Versions

| Version | Content | Status |
| --- | --- | --- |
| v1 | Single symbol; limit + market + cancel; price-time priority; integer ticks; simple data structures; text-file input | Design in step 07 |
