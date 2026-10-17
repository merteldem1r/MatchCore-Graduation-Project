# 03 · Project Name & Repository Structure

> **Status:** in progress (structure done, name not chosen yet) · **Date:** 2026-10-17

## Goal of this step

1. Give the repository a clean structure that supports working step by step, day by day.
2. Choose a **codename** for the project and draft the **formal academic title** (Turkish + English).

## Content

### Repository structure

```text
Graduation-Project-CENG/
├── README.md                 ← project overview, current status, map of the repo
├── AGENTS.md                 ← instructions for AI coding agents (Claude, Codex, …)
├── CLAUDE.md                 ← imports AGENTS.md (Claude Code reads this file)
│
├── docs/
│   ├── README.md             ← index of all documentation
│   ├── steps/                ← the project's story, one numbered document per step
│   │   ├── 01-project-idea.md
│   │   ├── 02-how-an-exchange-works.md
│   │   ├── 03-name-and-repo-structure.md
│   │   └── interactive/      ← HTML versions of steps (e.g. the order-book simulator)
│   ├── application/          ← about the software itself
│   │   ├── README.md         ← what the system is: components, scope, version history
│   │   ├── PLAN.md           ← the step plan with target dates and status
│   │   └── resources.md      ← research material, similar projects, references
│   ├── memory/               ← shared project state for AI agents (and humans)
│   │   ├── project-state.md  ← where we are, what's next, open questions
│   │   └── decisions.md      ← decision log: what, when, why, alternatives
│   ├── rules/                ← conventions every contributor/agent follows
│   │   ├── collaboration.md  ← how we work (step by step, simple first…)
│   │   └── academic.md       ← academic integrity, data/ethics, writing rules
│   └── graduation-project/   ← university material
│       ├── README.md         ← deadlines + what each document asks for
│       ├── guidelines/       ← official guidelines and calendar
│       ├── templates/        ← official .docx templates (unchanged)
│       └── submissions/      ← our filled-in forms (proposal, interim report, thesis)
│
└── src/                      ← code (not started; nothing is written before step 07)
    ├── engine/               ← C++ matching engine (the core)
    ├── server/               ← Go service: reads engine output, serves the dashboard
    └── client/               ← web dashboard (read-only)
```

**Why it is laid out this way**

- **Separation of concerns.** `steps/` is *the journey* (learning + decisions over time). `application/` is *the product* (what the system is now). `graduation-project/` is *the university side* (rules, templates, our submissions). `memory/` + `rules/` are *context for agents*.
- **One place for each kind of information**, so nothing has to be updated in two places:
  - deadlines → `graduation-project/README.md`
  - step plan and status → `application/PLAN.md`
  - current state → `memory/project-state.md`
  - decisions → `memory/decisions.md`
- **`src/` grows only when needed.** Build files, tests and benchmarks are added in the step where they are first needed, not upfront.

**Small fixes made**

- `docs/application/resourses.md` → `resources.md` (typo).
- `.claude/settings.json` was empty, which is not valid JSON; it now contains `{}`.

**What is deliberately not in the repo**

- The student number and other personal details, and the advisor's private emails (only a summary of the agreed scope is kept). The repository may become public.

### Codename brainstorm

A codename is the short name used for the repository, the code and the presentation (like *LMAX*, *Kafka*, *Redis*). The formal title is separate and goes on the forms.

**What makes a good codename here**

- short (1–2 syllables, easy to say in a 10-minute presentation);
- connected to the idea of matching, balance, speed, order or determinism;
- not the name of a real exchange or trading product (no *Kraken*, *Matchbook*, *Liquid*, *Nasdaq*…);
- works for both a Turkish and an international audience.

| Codename | Meaning / story | Notes |
| --- | --- | --- |
| **Terazi** | Turkish for *scales / balance*. A matching engine literally weighs bids against asks. | Memorable for a Turkish jury; easy logo (⚖). |
| **Mizan** | *Balance, scale*. In Turkish accounting, *mizan* is the trial balance, a finance connection. | Short, elegant. |
| **Crossbook** | *Cross* (the moment a buy and sell match) + *order book*. | Descriptive; says what it does. |
| **Tickforge** | *Tick* (price step) + *forge*: a precisely engineered engine. | Sounds like a tool. |
| **Kantar** | Turkish *weighbridge*: weighs quantities. | Less elegant than Terazi. |
| **Fifo** | The core rule of time priority. | Playful; maybe too generic. |
| **Sıra** | Turkish *queue / turn / order*. Every order waits its turn. | Very short; dotless ı can be awkward in code. |
| **Kestrel** | A small falcon that hovers perfectly still, then strikes fast: precision + speed. | Common name in software. |
| **Replay** | Hints at deterministic replay, the fault-tolerance idea. | Clever, but a common word. |

*Status: not chosen yet. Add your own ideas to this table.*

### Formal title drafts (finalized in step 08 with the proposal form)

1. **EN:** Design and Performance Analysis of a Low-Latency, Deterministic Order Matching Engine
   **TR:** Düşük Gecikmeli ve Deterministik Bir Emir Eşleştirme Motorunun Tasarımı ve Performans Analizi
2. **EN:** A Deterministic, Fault-Tolerant Order Matching Engine: Data Structure Trade-offs and Latency Analysis
   **TR:** Deterministik ve Hataya Dayanıklı Bir Emir Eşleştirme Motoru: Veri Yapısı Seçimleri ve Gecikme Analizi
3. **EN:** A Low-Latency Order Matching Engine with Deterministic Replay
   **TR:** Deterministik Yeniden Oynatma Destekli Düşük Gecikmeli Emir Eşleştirme Motoru

A codename can be combined with a title: *"Terazi: Design and Performance Analysis of …"*.

Turkish terms used: *emir* = order, *emir defteri* = order book, *eşleştirme motoru* = matching engine, *gecikme* = latency.

## Decisions

- Repository structure as above (2026-10-17).
- Step documents live in `docs/steps/`; HTML versions are kept in `docs/steps/interactive/`.
- `docs/memory/` and `docs/rules/` are context for AI agents.

## Open questions

- **Codename**: pick one (or a new idea).
- **Formal title**: which draft direction feels right? It depends on what we emphasize (performance vs. fault tolerance), which step 07 settles.

## Next step

→ 04 · Why this is a computer engineering problem: latency vs. throughput, percentiles (p50 / p99 / p99.9), what makes code fast or slow (allocation, cache misses), which data structure fits which operation, and why determinism matters.
