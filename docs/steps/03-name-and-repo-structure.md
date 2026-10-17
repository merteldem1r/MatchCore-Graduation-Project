# 03 · Project Name & Repository Structure

> **Status:** done · **Date:** 2026-10-17

## Goal of this step

1. Give the repository a clean structure that supports working step by step, day by day.
2. Choose a **codename** for the project and draft the **formal academic title** (Turkish + English).

## Content

### Repository structure

```text
Graduation-Project-CENG/
├── README.md                 ← project overview, current status, map of the repo
├── AGENTS.md                 ← instructions for AI coding agents (Claude, Codex, …)
├── CLAUDE.md                 ← imports AGENTS.md + Claude-only notes
├── .githooks/commit-msg      ← refuses commits that don't follow type(scope): description
├── .github/workflows/        ← CI: checks commit messages on every push
├── .claude/
│   ├── settings.json         ← denies git add/commit/push for Claude
│   └── skills/               ← /step and /wrap-up workflows
│
├── docs/
│   ├── README.md             ← index of all documentation
│   ├── steps/                ← the project's story, one numbered document per step
│   │   ├── README.md         ← step list + status (the only place it's tracked)
│   │   ├── 01-project-idea.md
│   │   ├── 02-how-an-exchange-works.md
│   │   ├── 03-name-and-repo-structure.md
│   │   └── interactive/      ← HTML versions of steps (e.g. the order-book simulator)
│   ├── application/          ← about the software itself (living docs)
│   │   ├── README.md         ← what the system is: components, scope
│   │   ├── ROADMAP.md        ← versions v1 → v5 + milestones
│   │   ├── engine/           ← README (status) + vN-design.md + benchmarks.md, as they are written
│   │   ├── server/
│   │   └── client/
│   ├── research/             ← literature, similar projects, data sources (resources.md)
│   ├── memory/               ← shared project state for AI agents (and humans)
│   │   ├── project-state.md  ← where we are, what's next, open questions
│   │   └── decisions.md      ← decision log: what, when, why, alternatives
│   ├── rules/                ← conventions every contributor/agent follows
│   │   ├── collaboration.md  ← how we work (step by step, simple first…)
│   │   ├── academic.md       ← academic integrity, data/ethics, writing rules
│   │   └── git-and-recap.md  ← only the student uses git; recap after every change
│   └── graduation-project/   ← university material
│       ├── README.md         ← deadlines + what each document asks for
│       ├── guidelines/       ← official guidelines and calendar
│       ├── templates/        ← official .docx templates (unchanged)
│       └── submissions/      ← our filled-in forms (proposal, interim report, thesis)
│
└── src/                      ← code (not started); READMEs hold build/run instructions only
    ├── engine/               ← C++ matching engine (the core)
    ├── server/               ← Go service: reads engine output, serves the dashboard
    └── client/               ← web dashboard (read-only)
```

**Why it is laid out this way**

- **Separation of concerns.** `steps/` is *the journey* (learning + decisions over time). `application/` is *the product* (what the system is now). `graduation-project/` is *the university side* (rules, templates, our submissions). `memory/` + `rules/` are *context for agents*.
- **One question → one home.** Every kind of information has exactly one file; other places only link to it, so nothing drifts:

  | Question | Single home |
  | --- | --- |
  | Where are we right now? | `memory/project-state.md` (rewritten each session, < ~40 lines) |
  | Why did we choose X? | `memory/decisions.md` (one shared log with an Area column) |
  | What did we learn / how did we get here? | `steps/NN-*.md` |
  | Which steps, what status? | `steps/README.md` |
  | What is the system? Which versions, when? | `application/README.md`, `application/ROADMAP.md` |
  | How does the engine / server / client work? | `application/<component>/` |
  | How to build and run the code? | `src/<component>/README.md` |
  | Which sources and similar projects? | `research/` |
  | University deadlines and forms? | `graduation-project/` |

- **Memory stays abstract.** It only says *where we are* and *what we decided*, never component details, because it is loaded in every session.
- **`src/` grows only when needed.** Build files, tests and benchmarks are added in the step where they are first needed, not upfront.

### How AI context is organized (four layers)

| Layer | What | Loaded |
| --- | --- | --- |
| 1 · Always | `CLAUDE.md` → `AGENTS.md`, which imports `docs/rules/*` and `docs/memory/project-state.md` | Every session. Keep these files **short**, since everything here costs context each time. |
| 2 · Skills | `.claude/skills/step` (work on a step), `.claude/skills/wrap-up` (end a session) | Only when used |
| 3 · Reference | `docs/steps/`, `docs/application/`, `docs/graduation-project/` | Only when needed |
| 4 · Private | Claude's own memory outside the repo | Personal preferences only, never project facts |

The **repository is the source of truth** for project memory (`docs/memory/`): versioned, readable by any tool or person. Rules live in `docs/rules/` (not `.claude/rules/`) so they work for every AI tool. Path-scoped Claude rules (e.g. for `src/engine/`) can be added once code exists.

**Daily routine:** `/step` → discuss → step document → `/wrap-up` → recap + suggested git commands → the student commits.

**Small fixes made**

- `docs/application/resourses.md` → `resources.md` (typo).
- `.claude/settings.json` was empty, which is not valid JSON; it now contains `{}`.

**What is deliberately not in the repo**

- The student number and other personal details, and the advisor's private emails (only a summary of the agreed scope is kept). The repository may become public.

### Codename: **MatchCore** ✅

Chosen on 2026-10-17: *the core that matches orders*, which says exactly what the project is. Note: an open-source Rust crate named `matchcore` exists (a deterministic price-time matching engine). Ours is an independent C++ project; the crate is listed in `docs/research/resources.md` as a similar project to study and cite.

### Codename brainstorm (for the record)

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

Also considered: QuantCore (rejected: "quant" means the trading-strategy side, sounds like "quantum", and the name is used by several companies), TickCore, BookCore.

### Formal title drafts (finalized in step 08 with the proposal form)

1. **EN:** Design and Performance Analysis of a Low-Latency, Deterministic Order Matching Engine
   **TR:** Düşük Gecikmeli ve Deterministik Bir Emir Eşleştirme Motorunun Tasarımı ve Performans Analizi
2. **EN:** A Deterministic, Fault-Tolerant Order Matching Engine: Data Structure Trade-offs and Latency Analysis
   **TR:** Deterministik ve Hataya Dayanıklı Bir Emir Eşleştirme Motoru: Veri Yapısı Seçimleri ve Gecikme Analizi
3. **EN:** A Low-Latency Order Matching Engine with Deterministic Replay
   **TR:** Deterministik Yeniden Oynatma Destekli Düşük Gecikmeli Emir Eşleştirme Motoru

The codename can be combined with a title: *"MatchCore: Design and Performance Analysis of …"*.

Turkish terms used: *emir* = order, *emir defteri* = order book, *eşleştirme motoru* = matching engine, *gecikme* = latency.

## Decisions

- Repository structure as above (2026-10-17).
- Step documents live in `docs/steps/`; HTML versions are kept in `docs/steps/interactive/`.
- `docs/memory/` and `docs/rules/` are context for AI agents; four-layer setup with `/step` and `/wrap-up` skills.
- Codename: **MatchCore**.
- Only the student uses git; Claude is technically blocked from `git add` / `commit` / `push`.
- One question → one home (table above).
- Commit messages follow Conventional Commits (≤ 100 chars), enforced by a git hook + CI.

## Open questions

- **Formal title**: which draft direction feels right? It depends on what we emphasize (performance vs. fault tolerance), which step 07 settles.

## Next step

→ 04 · Why this is a computer engineering problem: latency vs. throughput, percentiles (p50 / p99 / p99.9), what makes code fast or slow (allocation, cache misses), which data structure fits which operation, and why determinism matters.
