# 08 · Proposal Form

> **Status:** in progress (ready to send) · **Date:** 2026-10-26

## Goal of this step

Fill in the official proposal form (*Bitirme Projesi Başvuru Formu*) and send it to the department before the 2026-11-01 deadline.

## Content

### Why this step came before 04–07

Steps 04–07 were planned to come first, but on 2026-10-26 they had not started and the deadline was six days away. The form only needs decisions that were already made: topic and scope (D1, D2), stack (D4), the v1 sketch (D5) and the version plan in [`../application/ROADMAP.md`](../application/ROADMAP.md). So the form was written first. Steps 04–07 still happen after the submission; they explain the topic in depth and feed the interim report and the thesis.

### What the form asks for and where each answer came from

| Field | Answer | Source |
| --- | --- | --- |
| Title (TR + EN) | *Düşük Gecikmeli ve Deterministik Emir Eşleştirme Motoru* / *A Low-Latency, Deterministic Order Matching Engine* | Chosen this step (D14) |
| Members, date, advisor | In the `.docx` only (personal data) | — |
| Purpose (*Projenin Amacı*) | What a matching engine is, the three goals (deterministic, fast, crash-safe), why it is a computer engineering problem, what it is not | Steps 01–02 |
| Methods (*Yöntem ve Metodlar*) | Simple-first, five measured versions; software and hardware tools | D4, D5, roadmap |
| Timeline | October to December 2026 (BIL 497), February to May 2027 (BIL 498) | Roadmap |
| References | Three core sources | [`../research/resources.md`](../research/resources.md) |

The full text is in [`../graduation-project/submissions/01-proposal-form.md`](../graduation-project/submissions/01-proposal-form.md).

### Writing style

The form uses Turkish sentences, but every technical term stays in English: *engine*, *order book*, *price-time priority*, *latency*, *data structure*, *crash recovery*, *event log*. A first draft translated them (*motor*, *emir defteri*), which read less naturally than the terms the field actually uses. It is written plainly, the way a student would explain the project, not like a research paper. It stays short and only promises what the roadmap already plans. The web framework is left open on purpose, since a proposal can change later.

### How it is sent

Email to `bm@medeniyet.edu.tr`, subject `BIL497: proje başvuru formu`, with the advisor in CC (then no signature is needed), and the PDF export of the form attached. Before that, the form also goes to the advisor for a quick review, together with a short progress update.

## Decisions

- D14: title, option A.
- D15: university documents use Turkish sentences with English technical terms; the title is in Turkish and English.
- D16: the proposal was written before steps 04–07, which move to after the submission.
- D17: the reference list is cut down to three core sources.
- D18: filled `.docx` forms are kept out of git; their Markdown source is committed.

## Open questions

- None for the form itself; see the open questions in [`../memory/project-state.md`](../memory/project-state.md).

## Next step

→ Send the form and mark this step done. Then 04 · Why this is a computer engineering problem.
