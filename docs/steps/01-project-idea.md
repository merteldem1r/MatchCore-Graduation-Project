# 01 · Project Idea

> **Status:** done · **Date:** 2026-10-16 · **Interactive version:** [interactive/01-project-kickoff.html](interactive/01-project-kickoff.html)

## Goal of this step

Agree on *what* the graduation project is, why it was chosen, and how big it should be, based on the first exchange of emails with the advisor.

## Content

### The idea in one sentence

Build a **low-latency, deterministic order matching engine**: the program at the core of an exchange that pairs incoming buy and sell orders using **price-time priority**. Then use it to **measure and compare data structures**, and make it **recover exactly from crashes**.

### Why this topic

- It brings together the areas of computer engineering the project wants to explore: **algorithm analysis**, **data structures**, **performance engineering**, **system design**, and some **distributed systems / fault tolerance**.
- It is **measurable**. Every design choice can be backed by numbers (throughput, latency percentiles, memory, allocations).
- Prior experience in a company working in the HFT (high-frequency trading) domain made the problem familiar and motivating.

### What a matching engine is, in short

An exchange receives orders such as *"buy 100 shares at ≤ ₺50.00"* and *"sell 50 at ≥ ₺50.10"*. Orders that cannot trade yet wait in the **order book**. When a buyer's price meets a seller's price, the engine creates a **trade**. Best price goes first, and at the same price the earliest order goes first. → Full explanation in [02 · How an exchange works](02-how-an-exchange-works.md).

### Scope agreed with the advisor

The advisor approved the topic and suggested this scope, so the project can realistically be finished in two semesters:

| Part | Priority | What it means |
| --- | --- | --- |
| **Deterministic matching engine** | Core | Price-time priority; add, cancel, modify / cancel-replace; the order book's invariants always hold. |
| **Data structure comparison** | Core, framed as research questions | Order-id lookup (hash table vs. direct index); price-level index (balanced tree, skip list, radix tree, bitmap/array); FIFO queue per level with O(1) cancel (intrusive / indexed list). Not all of them are mandatory. |
| **Benchmarks & performance analysis** | Core | Throughput; p50 / p99 / p99.9 / max latency; memory use; allocation count. Workloads: high cancel ratio, aggressive market orders, narrow vs. wide price ranges. |
| **Fault tolerance, part 1** | Core | Event log + snapshot + deterministic replay. |
| **Raft-based replication** | Optional, second phase, minimal | Full Raft could become a separate project on its own, so it is not a main requirement. |
| **Web dashboard** | Nice extra | Read-only view of order book depth and latency metrics. Must be a separate layer that never slows the engine. |

### Ground rules

- **No proprietary material.** No code, data or internal designs from any previous employer. Only **synthetic** or **openly licensed** data.
- **Start simple.** Version 1 is the simplest correct engine. Every improvement after that is a measured upgrade on top of it.

### What we are *not* building

- A full exchange (no user accounts, no real money, no clearing or settlement, no real network gateway in v1).
- Raft as a core requirement.
- The alternative idea (comparing quantum vs. classical algorithms for financial matrix computations). The advisor found it interesting but considered it a separate research project, and judged the matching engine more mature, system-oriented and measurable.

### How this fits the grading

The jury's evaluation form (100 points) gives weight to things a working engine alone does not show:

| Criterion | Points | How this project earns it |
| --- | --- | --- |
| Methods chosen correctly, modern tools | 10 | Justified data structure choices, proper benchmarking methodology |
| Originality | 10 | A clear original contribution (to be defined in step 07) |
| Problem definition & modelling | 10 | Order book model, invariants, determinism |
| Interpreting results | 10 | Benchmark analysis across workloads and versions |
| Literature (5) + references (10) | 15 | Steps 05 and onward: real sources, cited properly |
| Formatting | 10 | Follow the thesis writing guide |
| Goals, fit to programme, ethics | 15 | Clear goals; no proprietary material |
| Presentation | 20 | Live demo (dashboard), clear explanation |

### Constraints

- **Solo project**: one student, two semesters (BIL 497 in fall 2026, BIL 498 in spring 2027).
- **Deadlines**: see [`../graduation-project/README.md`](../graduation-project/README.md). The proposal form is due by **2026-11-01**.

## Decisions

- Topic: order matching engine (alternative idea dropped).
- Scope: as in the table above. Raft is optional.
- Direction for the tech stack (not final until step 06): **C++** for the core engine and its measurements; **Go + a web frontend** for the dashboard side.
- Working style: step by step, simplest version first.

## Open questions

- Advisor's full title and name (for the forms).
- Will the advisor require the interim report? (Assume yes.)
- Spring 2027 calendar, which gives the thesis deadline (not published yet).

## Next step

→ [02 · How an exchange works](02-how-an-exchange-works.md)
