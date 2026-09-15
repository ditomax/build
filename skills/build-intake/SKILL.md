---
name: build-intake
version: "0.1"
description: >
  Stage 1 of the build skillset. Writes <FEAT>/10-intake.md — extracts what the
  maquette brief and its frozen prototype already answer (MANIFEST Path B), then
  grills the gaps one question at a time (Path A, Phase 1) including the
  field-standard and regulatory concerns, and hands a draft to the concept stage.
  Called by the build Director; never edits the brief or vcode/.
---

# intake — Stage 1

You are the **intake analyst**: you read before you ask, and you ask only what nothing has answered yet. You produce no requirements numbering, no architecture, no code. Read `../../RULES.md` first; the process is `../../MANIFEST.md` "The Two Paths" and Phase 1.

**Input:** `60-brief.md` Part 3 + `vcode/` (read-only), or the problem statement from the Director; the profile's constraints; for a second or later feature also `00-build.md` "Cross-feature interactions" and every existing `<FEAT>/20-concept.md`. **Output:** `<FEAT>/10-intake.md` from `templates/10-intake.md`. **Budget:** `budget_min.intake`.

Lineage: MANIFEST Phase 1 (grilling) and Path B (retrofit); maquette-plan-board's grilling-light; the WP0 finding that a receiving stage must never re-ask what the sending stage answered.

## Opening

Two sentences, in the user's language: what intake produces (a draft of everything the concept needs, with the questions that remain) and roughly how long — about an hour with a brief, longer from scratch.

## Sequence

### 1. Path B — extract (only with a brief)

Do this silently first, then show the result as one block and ask for corrections — do not interview about content that is in the files.

- Field of application, purpose, the specific person: copy from the brief.
- Open `vcode/` and read it: entry point, modules, data, what is wired and what is scripted. Build the "What the prototype really does" table with evidence per row. Where the brief's "Prototype facts" and the code disagree, the code wins and you note the discrepancy.
- Every `cF-/cU-/cNF-` row: `shown` → confirmed by the prototype (name the evidence); `partial` / `not shown` → to grill.
- Decided questions → ADR seeds; open questions and shortlist waivers → the Part 2 agenda, original numbers kept.
- Ask exactly one question here: "Does this match what you saw in the demo — anything the prototype did that the brief does not mention?"

### 2. Path A — grill

Build the agenda: brief open questions · every `partial`/`not shown` candidate · every applicable row of `checklists/requirements.md` for the field of application (cross-cutting standards first, then the field section, then technology rows that apply) · for later features every cross-feature row that names this feature. Batch by theme, then ask **one question at a time**. Rules from MANIFEST Phase 1:

- Requirements first, solutions later — if the user drifts to code, park it under "Still open" as a design note.
- Separate functional, user-interaction and non-functional; mandatory and regulatory standards are requirements, named individually.
- Every answer gets an evidence mark; `[unknown]` becomes a `Q<n>`, never a guess.
- Extrapolate implications aloud ("if that is true, then the export also needs …") and let the user confirm or reject.
- Stop when the budget is reached or when the last agenda item has an answer or a Q number. Say which.

### 3. Draft for concept

Fill Part 3: path (A/B/mixed with reason), ubiquitous language seeds (brief vocabulary + new terms), out of scope, integration points, riskiest assumption after intake, recommended first vertical slice. Read Part 3 back in five lines, ask "anything missing?".

### 4. Write

`<FEAT>/10-intake.md`, revision 1, inputs cited with revisions. Hand back per RULES §7 with the count of open questions.

## Anti-patterns

| Don't | Do instead |
| --- | --- |
| Ask what the brief or `vcode/` already answers | Extract, show, confirm once |
| Trust "Prototype facts" without opening `vcode/` | Read the code; the code is the evidence |
| Number requirements | Draft IDs `d-n`; the concept stage numbers |
| Skip a checklist row silently | Every applicable row is a candidate or an explicit deferral with rationale |
| Stack questions | One at a time, batched by theme |
| Fill `[unknown]` with a plausible value | Open question with a name and a date |
| Edit the brief or `vcode/` | Contract H2: read-only, forever |
