# Decision Log

> One row per decision. Newest at the bottom. Big decisions get a short "why" and the alternatives considered, which helps when writing *Materyal ve Yöntem* in the thesis.

| # | Date | Decision | Why | Alternatives considered | Step |
| --- | --- | --- | --- | --- | --- |
| D1 | 2026-10-16 | Topic: low-latency deterministic order matching engine | Measurable, system-focused, matches interests and experience; approved by the advisor | — | [01](../steps/01-project-idea.md) |
| D2 | 2026-10-16 | Scope: matching + data structure comparison + benchmarks + event log/replay are core; Raft optional; dashboard read-only extra | Advisor's guidance; full Raft would be a separate project | Raft as a core requirement | [01](../steps/01-project-idea.md) |
| D3 | 2026-10-16 | Synthetic or openly licensed data only; no proprietary material | Academic and professional ethics | — | [01](../steps/01-project-idea.md) |
| D4 | 2026-10-16 | Tentative stack: C++ engine; Go + web for dashboard | Control over memory/latency in C++; student's web/Go experience | Go or Rust for the engine (to revisit in step 06) | [01](../steps/01-project-idea.md) |
| D5 | 2026-10-17 | v1 sketch: one symbol, limit + market + cancel, price-time priority, integer ticks, single-threaded | Simplest correct starting point; everything else is a measured upgrade | — (confirm in step 07) | [02](../steps/02-how-an-exchange-works.md) |
| D6 | 2026-10-17 | Repository structure: `docs/steps`, `docs/application`, `docs/memory`, `docs/rules`, `docs/graduation-project`, `src/{engine,server,client}` | Separate journey / product / university / agent context | — | [03](../steps/03-name-and-repo-structure.md) |
| D7 | 2026-10-17 | Codename **MatchCore** | Says exactly what it is; easy to say | QuantCore (wrong connotation, name in use), Terazi, Mizan, TickCore, Crossbook | [03](../steps/03-name-and-repo-structure.md) |
| D8 | 2026-10-17 | Four-layer AI context: AGENTS.md imports rules + project state; `/step` and `/wrap-up` skills; repo is the source of truth | Same context every session, for every tool, kept short | `.claude/rules/` only (Claude-specific) | [03](../steps/03-name-and-repo-structure.md) |
