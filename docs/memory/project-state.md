# Project State

> **Where we are right now, nothing else.** Rewrite this file at the end of each session; never append history. Keep it under ~40 lines. History lives in `docs/steps/`, `decisions.md` and git; component details live in `docs/application/<component>/README.md`.

**Last updated:** 2026-11-25

## Current step

**04 · Why this is a computer engineering problem**: in progress, topics 1–3 of 5 done (data structures, latency, determinism). Next: topics 4 (crash recovery) and 5 (concurrency), planned together in one session, which finishes step 04.

## Recently done

- Step 04 topics 1–3 written up in `docs/steps/04-why-a-computer-engineering-problem.md` and its interactive page (live order book, percentile explorer, queue simulator, determinism lab), checked with Playwright on desktop, dark mode and phone.
- Liquibook studied as a reference (multimap per side, linear cancel within a price level); BSD-style license checked. No code reused.
- Short Turkish status email to the advisor drafted (Liquibook, ByteMonk HFT video, LMAX article).

## Components

Engine, server, client: not started. See `docs/application/README.md`.

## Next actions

1. Finish step 04 (topics 4 + 5) in one session.
2. Step 05 (similar projects): keep it short; Liquibook, LMAX and the HFT video are already studied.
3. Step 06 (tech stack): mostly confirm the proposal (C++20, CMake, GoogleTest, Google Benchmark, HdrHistogram); pick the web framework later.
4. Step 07: v1 scope and design. Then build v1 in small runnable pieces (~2026-12-07 → 12-20): project + first test, book, matching, cancel, file in / events out with golden files, determinism check.
5. End of term: small working prototype + short presentation to the advisor after finals (D20).
6. Arrange weekly ~30-minute advisor meetings. On a new clone: `git config core.hooksPath .githooks`.

## Open questions

| Question | Needed for | Status |
| --- | --- | --- |
| Advisor's feedback on the form | Scope check before step 07 | None yet; repository approved as a starting point |
| Spring 2027 calendar (thesis deadline) | Spring planning | Not published yet |
| Web frontend framework | Dashboard (v5) | Open; decide in step 06 or later |
| Design questions for v1 (price-level container, order-ID index, input message fields) | Step 07 | Listed in step 04 "Open questions" |

## Context

Solo project. The student is a web developer with Go and C++ experience, new to the market domain, so explain from first principles; each topic starts with its key words in plain language. Stack: C++20 engine; Go + web dashboard. University documents: simple and plain, in a student's voice; Turkish sentences, English technical terms.
