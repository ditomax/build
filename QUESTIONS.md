# build — Every question the skillset asks a human

**Version 1 (build 0.1.3).** One row per question, in the order it is asked. **The IDs are stable identifiers** — `profile/questions.md` (setup skill) refers to them; renumbering is a breaking change. With a brief (contract H2) the Path B rows are extracted from `60-brief.md` and `vcode/` and confirmed in one block, never re-asked. Grilling questions are agenda-driven — the list names the sources of the agenda, not every item, since those depend on the field of application. Three gate questions go to the sponsor.

## Director (`build`) — first call only

| # | Question | Prefilled from | Note |
| --- | --- | --- | --- |
| D1 | (several briefs) Which one do we build? — listed as code · title · recommendation | `planning/maquette/*/60-brief.md` Part 4 | exactly one |
| D1' | (cold start) What are we building — the problem in two sentences, and for whom? Or, if you come from maquette, the path to the maquette folder | — | path A; if the first message mentions maquette / brief / prototype, the path is asked first |
| D2 | Feature code (2–4 letters) and title — proposed, user corrects | maquette code | |
| D3 | Path: mixed (B on what the maquette showed, A on the rest) — or A or B? | proposed | cold start: A |
| D4 | Field of application — <inferred>? | brief Part 3 (with brief: not asked) | |
| D5 | Code root: the project folder outside `planning/` — right? (standalone: where?) | project layout default | never inside `<work>` |
| D6 | Git: is the folder a repository? | checked silently, result told | never `git init`; a clone of the public repo counts as no |
| D7 | Language of the result files? (requirements, IDs, code, commits stay English) | profile → chat language → explicit statement | |

Later calls: "Good as it is — next, or redo?" after a stage; the gate questions below; on a concept redo after code exists: "this is a concept version bump, committed before code follows — go?"

## Stage 1 — intake

| # | Question | Path | Prefilled from | Intake section |
| --- | --- | --- | --- | --- |
| I0 | Here is what the brief and the prototype already answer — does this match what you saw in the demo, anything the prototype did that the brief does not mention? (one block) | B | brief Part 1 (purpose, person), Part 2 (cannot rows), Part 3 (field, guardrails, candidates with status, vocabulary, decided/open questions, riskiest assumption, prototype facts incl. slices and design), Part 4 (sponsor), `vcode/` read | Part 1 |
| I1 | Per open question from the brief (original numbers): … | A | brief "Open questions" (incl. shortlist waivers) | Part 2 agenda |
| I2 | Per candidate marked partial / not shown: what exactly must it do, and how would we know? | A | brief candidate table | Part 2 |
| I3 | Per applicable row of `checklists/requirements.md` for the field: does this apply — as a requirement, or deferred with a reason? (cross-cutting standards first, then field, then technology) | A | field of application | Part 2 field-standard concerns |
| I4 | Per cross-feature row naming this feature (later features only): how do we interact, what could we break? | A | `00-build.md` cross-feature table, other concepts §0/§9 | Part 2 |
| I5 | Implication check: if that is true, then … also needs — right? | A | — | Part 2 |
| I6 | Part 3 in five lines (path, vocabulary seeds, out of scope, integration points, riskiest assumption, first slice) — anything missing? | | — | Part 3 |

Cold start: I0 is replaced by D1' and the grilling runs on the full Phase 1 agenda (purpose, actors, functional / user-interaction / non-functional, field concerns, standards).

## Stage 2 — concept

| # | Question | When | Concept section |
| --- | --- | --- | --- |
| C1 | This requirement has no test I can write — is it a requirement, or a wish? | numbering, per untestable row | §3–§5 |
| C2 | Open question OQ-<n> (batched by theme, one at a time): decision in one line? | resolve | §8, ADR |
| C3 | I need to research <dependency / existing code> for this — go ahead? | resolve, when needed | §8 |
| C4 | Per existing component or feature we touch: how do we use it, what could we break, what must change, backward-compatible? (derived from the checklist for the field) | interactions | §9–§10 |
| **Gate PM** | Requirements, decisions, interactions and schema are ready (§0–§10) — approved, changes requested, or back to intake? | after §10, asked by the Director to the sponsor | Gates table |
| C5 | Path B module: rebuild from concept or adapt from `vcode/` — and why? | architecture, per module | §11 |
| **Gate 6** | Consistency matrix: <n> MISMATCH / DANGLING — accept (with rationale), fix, or defer? | after §12, Director → sponsor | Gates table |

## Stage 3 — synthesis

| # | Question | When |
| --- | --- | --- |
| Y1 | Test plan ready (§13) — start slice S1? | before any code |
| Y2 | The concept does not name this dependency / stack choice: <options> — which? | when it arises |
| Y3 | Slice S<n> is green (<n/m tests>, committed) — next slice, or stop here? | after every slice |
| Y4 | Coding found a requirement the concept lacks: <…> — I have logged it; concept redo now or after this slice? | on discovery |
| **Gate 9** | Gap matrix: <n> PARTIAL / MOCKED / MISSING — accept (with rationale), back to synthesis, or defer with a follow-up? | after the last slice, Director → sponsor |
| Y5 | Gate clean — release <version>? | release point |

## Stage 4 — rescue (on demand)

| # | Question | When |
| --- | --- | --- |
| R1 | Ranked opportunities (table shown) — which ones, in this order? | once per round |
| R2 | Kept refactors change concept sections <…> — schedule the concept redo now? | reconcile |

## What build never asks

Anything in brief Parts 1–4 or visible in `vcode/`: purpose, the specific person, the field, the guardrails, what the prototype shows, its slices and acceptance criteria, its stack and data, the shared vocabulary, decided questions, the riskiest assumption's state, the sponsor. If intake asks one of these, the H2 contract is broken — report it, do not work around it.
