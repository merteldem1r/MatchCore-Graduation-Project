# MatchCore — Roadmap

> **The product's roadmap**: which versions we build and when, plus the university milestones. Learning/decision steps are tracked in [`../steps/README.md`](../steps/README.md); deadline details are in [`../graduation-project/README.md`](../graduation-project/README.md).

## Milestones

| When | Milestone |
| --- | --- |
| by 2026-11-01 | Proposal form sent |
| ~2026-12-14 → 12-20 | Interim report (if requested) |
| Spring 2027, end of week 13 | Thesis submitted |
| End of spring 2027 | Jury presentation |

## Versions (tentative; confirmed in step 07)

Each version is a measured upgrade of the previous one.

| Version | What it adds | Component | Target |
| --- | --- | --- | --- |
| **v1** | Correct basic engine: one symbol, limit + market + cancel, price-time priority, integer ticks, standard containers, text-file input → printed events | engine | Nov 2026 |
| **v2** | Measurement: benchmark harness, workload generator, latency percentiles | engine | Dec 2026 (interim report) |
| **v3** | Faster data structures, compared against v1 (price-level array/bitmap, intrusive FIFO lists, order-id index) | engine | Feb–Mar 2027 |
| **v4** | Fault tolerance: event log + snapshots + deterministic replay | engine | Mar–Apr 2027 |
| **v5** | Read-only dashboard: Go server + web client | server, client | Apr 2027 |
| *optional* | Minimal replication (Raft) | engine | May 2027, if time allows |
