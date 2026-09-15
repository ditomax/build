---
stage: intake
owner: build-intake
status: in_progress          # open | in_progress | done | skipped
revision: 1
created: <YYYY-MM-DDThh:mm>
updated: <YYYY-MM-DDThh:mm>
input: <60-brief.md@<rev>, vcode/ (frozen) | problem statement (chat)>
feature: <FEAT>
path: <A | B | mixed>
---

# Intake <FEAT>: <title>

_Result of the intake stage. Part 1 is what the prototype and the brief already answer (Path B — extracted, not asked). Part 2 is what had to be grilled (Path A). Part 3 is the draft the concept stage starts from. Nothing here is ratified; the concept stage numbers and freezes._

## Part 1 — Extracted from the brief and the prototype (Path B)

<!-- Only with a brief. Without one, write "not relevant for this feature — cold start" in every Part 1 section. -->

### Field of application

<one line, from the brief — or inferred and confirmed: web app, backend service, mobile, embedded, hardware, lab equipment, desktop, data/ML>

### Purpose and the specific person

<from brief Part 1 and 10-seed.md Q3 — one paragraph, in the sponsor's words>

### What the prototype really does

<!-- From "Prototype facts" and reading vcode/. Facts, not judgement. -->

| Behaviour | In vcode/ | Real / faked / scripted | Evidence (file, line or screen) |
| --- | --- | --- | --- |
| <…> | <…> | <…> | <…> |

### Candidate requirements taken over

<!-- Every cF-/cU-/cNF- row of the brief, with the maquette status. "shown" → confirmed by the prototype; "partial" / "not shown" → goes to Part 2. -->

| Brief ID | Requirement | Status in maquette | Intake verdict |
| --- | --- | --- | --- |
| cF-1 | <…> | shown | confirmed by vcode/ (<evidence>) |
| cF-<n> | <…> | not shown | to grill (Part 2) |

### As-built architecture sketch

<stack, entry point, modules or files, data shapes, external services — what a rebuild would reuse and what it would not; from "Prototype facts" and vcode/>

### Decided and open questions carried over

- **Decided:** <from brief "Resolved questions" — each becomes an ADR seed, listed with its original number>
- **Open:** <from brief "Open questions" — original numbers kept; this is the Part 2 agenda>

## Part 2 — Grilling (Path A)

<!-- MANIFEST Phase 1. One question at a time. Record every answer with its evidence mark. Requirements are not numbered yet — the concept stage numbers them. -->

### Agenda worked through

| # | Topic | Source | Answer (short) | Evidence |
| --- | --- | --- | --- | --- |
| 1 | <open question Q<n> / not-shown requirement / field-standard concern> | <brief | checklist | user> | <…> | <[evidenced] | [estimated] | [unknown]> |

### Field-standard concerns

<!-- checklists/requirements.md for the field of application: every applicable row becomes a candidate requirement or an explicit deferral with rationale. Mandatory and regulatory standards are requirements. -->

| Concern | Standard / check | Applies | Candidate requirement or deferral rationale |
| --- | --- | --- | --- |
| <…> | <…> | yes / no / deferred | <…> |

### New candidate requirements

| Draft ID | Requirement | Type | Origin | Evidence |
| --- | --- | --- | --- | --- |
| d-1 | <…> | F / U / NF | grilling #<n> | <…> |

### Still open after grilling

- **Q<n>** — <…> — <who can answer, by when>

## Part 3 — Draft for the concept stage

- **Path:** <A | B | mixed — and why>
- **Ubiquitous language seeds:** <terms from the brief's shared vocabulary plus new ones — goes to CONTEXT.md>
- **Out of scope (for now):** <…>
- **Integration points known:** <…>
- **Riskiest assumption after intake:** <from the brief, updated>
- **Recommended first vertical slice:** <one sentence — the smallest end-to-end path that proves the core>

## Notes (human)

<!-- Off limits for all skills. Copied verbatim on every rewrite. -->
