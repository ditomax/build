---
stage: intake
owner: build-intake
status: in_progress          # open | in_progress | done | skipped
revision: 1
created: <YYYY-MM-DDThh:mm>
updated: <YYYY-MM-DDThh:mm>
input: <60-brief.md@<rev> (contract H2/3 | H2/2), vcode/ (frozen) | 10-register.md@<rev> (register/1) | 10-shortlist.md@<rev> entry <ID> (H1/2) | problem statement (chat)>
feature: <FEAT>
path: <A | B | mixed | R | S | A+R | B+R | mixed+R>
---

# Intake <FEAT>: <title>

_Result of the intake stage. Part 1 is what the prototype, the brief, the register or the shortlist entry already answer (Path B, R, S — extracted, not asked). Part 2 is what had to be grilled (Path A). Part 3 is the draft the concept stage starts from. Nothing here is ratified; the concept stage numbers and freezes._

## Part 1 — Extracted from the brief, the prototype, the register or the shortlist entry (Path B · R · S)

<!-- Field of application, Purpose and the specific person, Guardrails taken over: with a brief, a register (Path R) or a shortlist entry (Path S). The prototype sections (What the prototype really does, Candidate requirements taken over, Deliberately-cannot rows, As-built architecture sketch) and Decided and open questions carried over: only with a brief; otherwise write "not relevant for this feature — <cold start | Path R without maquette | Path S>" (Path R / S carry their open questions in their own table). "Register trace" only with Path R, "Taken from the shortlist entry" only with Path S — otherwise the line "not relevant for this feature". -->

### Field of application

<one line, from the brief — or inferred and confirmed: web app, backend service, mobile, embedded, hardware, lab equipment, desktop, data/ML>

### Purpose and the specific person

<from brief Part 1 — one paragraph, in the sponsor's words>

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

### Guardrails taken over

<!-- Brief Part 3 "Guardrails", one per row. Each becomes a candidate NF requirement or an out-of-scope line in Part 3 below; none is re-asked. -->

| GR | Guardrail | Origin | Intake verdict |
| --- | --- | --- | --- |
| GR-1 | <…> | <…> | <cNF candidate | out of scope | profile constraint> |

### Deliberately-cannot rows (brief Part 2)

| Cannot | Reason category | Intake verdict |
| --- | --- | --- |
| <…> | <scope | time | data | decision pending> | <out of scope | to grill (decision pending) | requirement not shown> |

### As-built architecture sketch

<stack, entry point, modules or files, data shapes, external services, design choice — what a rebuild would reuse and what it would not; from "Prototype facts" and vcode/>

**Slices and acceptance criteria as built:** <from "Prototype facts" — seed for the integration scenarios in 30-synthesis.md §13>

### Decided and open questions carried over

- **Decided:** <from brief "Resolved questions" — each becomes an ADR seed, listed with its original number>
- **Open:** <from brief "Open questions" — original numbers kept; this is the Part 2 agenda>

### Register trace (Path R)

<!-- One row per R-ID: every Register row, every R-ID under the register's Out of scope, every item of origin maquette (`R-M<n>`). Verdict: → d-n (this feature) | → feature <CODE> | → OQ-n (question items) | deferred: <reason> | out: <register reason>; add "(open: OQ-n)" where a verdict depends on an open question. Origin-maquette items carry a proposed priority, confirmed at the PM gate. Priorities are the committee's — never changed here. A MUST without a verdict blocks the hand-back. The concept stage carries R-n → F-<FEAT>-n. -->

- **Register:** <file>@<rev> (<direct | via brief H2/3>) · **outcome:** <…> · **complete:** <yes | partial — …>
- **K.o. conditions → guardrails:** <R-n: … — one line each>

| Item | Priority | Statement (short) | Coverage in maquette | Verdict | Draft ID |
| --- | --- | --- | --- | --- | --- |
| R-<n> | <MUST | SHOULD | COULD> | <…> | <shown | faked | not shown (later) | out | —> | <→ d-n | → feature <CODE> | → OQ-n | deferred: … | out: …> | <d-n | —> |

**Count:** register items <n> · rows <n + maquette items> · MUST <m>: mapped <a> · other feature <b> · deferred <c> · open <0> · depending on an OQ <d>

### Taken from the shortlist entry (Path S)

<!-- Extracted silently from the entry, shown once, corrected by the user. Origin per row: entry <ID> · <field>. -->

| Field of the entry | Taken over as | Draft ID / OQ | Origin |
| --- | --- | --- | --- |
| <Opportunity · Cost of the status quo · Stakeholders & needs · Data situation · Personal data · V5 · V6 · V7 · K.o. V3 estimate · waiver V8 · Variants · What would tip it · …> | <purpose | baseline | person | candidate | guardrail | open question | I7/I8/I9 prefill | Part 3 (riskiest assumption, cheapest test) | out of scope (later feature) | nothing — gate passed> | <d-n | OQ-n | —> | entry <ID> · <field> |

## Part 2 — Grilling (Path A)

<!-- MANIFEST Phase 1. One question at a time. Record every answer with its evidence mark. Requirements are not numbered yet — the concept stage numbers them. -->

### Agenda worked through

| # | Topic | Source | Answer (short) | Evidence |
| --- | --- | --- | --- | --- |
| 1 | <open question OQ-<n> / not-shown requirement / field-standard concern> | <brief | checklist | user> | <…> | <[evidenced] | [estimated] | [unknown]> |

### Field-standard concerns

<!-- checklists/requirements.md for the field of application: every applicable row becomes a candidate requirement or an explicit deferral with rationale. Mandatory and regulatory standards are requirements. -->

| Concern | Standard / check | Applies | Candidate requirement or deferral rationale |
| --- | --- | --- | --- |
| <…> | <…> | yes / no / deferred | <…> |

### Operation

<!-- I7 — asked on every path; Path R / S: prefilled from the V6 note, read back first. Who operates the product permanently, per role. idea's V6 only settled a plausible operating model; names and time shares are settled here. Unknown → OQ-<n>; nobody for a role → tell the sponsor before concept. Operation duties that bind the product become cNF candidates below. -->

| Role | Name | Time share | Evidence |
| --- | --- | --- | --- |
| content / data upkeep | <…> | <…> | <…> |
| technology / hosting | <…> | <…> | <…> |
| user support | <…> | <…> | <…> |

### Running cost

<!-- I8 — asked on every path; Path R / S: prefilled from the V6 note, read back first. What makes running cost grow with use, who pays, what the heaviest user does. "Fixed running cost only" is a valid answer and skips checklists § Operating Cost; otherwise that section joins the field-standard concerns. Unknown → OQ-<n>. Guards that bind the product become cNF candidates below. -->

| Question | Answer | Evidence |
| --- | --- | --- |
| Cost drivers that grow with use (AI calls, documents, compute, metered services) | <… | fixed running cost only> | <…> |
| Who pays — price per user, fixed budget, nobody yet | <…> | <…> |
| Heaviest realistic user — what, how much | <…> | <…> |
| Monthly ceiling | <…> | <…> |

### Load and growth

<!-- I9 — asked on every path; Path R: prefilled from the frame's Users and scale, Path S: from the card's Users & scale — read back first. The figures every capacity decision rests on; they also fill the volume column of the concept's operating cost model. "Small and stable" with figures is a valid answer — it is the written reason to defer scale-out. Unknown → OQ-<n>. -->

| Quantity | At launch | In 1 year | In 3 years | Evidence |
| --- | --- | --- | --- | --- |
| Users (named / active per day; internal or external) | <…> | <…> | <…> | <…> |
| Concurrent users at peak | <…> | <…> | <…> | <…> |
| Peak pattern and factor (time of day, month end, campaign) | <…> | <…> | <…> | <…> |
| Data volume (records, documents, GB) | <…> | <…> | <…> | <…> |
| External limits (rate limits or quotas of services we call) | <…> | <…> | <…> | <…> |

### Losses and hazards (I11)

<!-- I11 — asked on every path, never skipped (RULES §8). Prefilled and read back first from: brief Part 3 guardrails, register K.o. conditions and V6 note, shortlist entry *Personal data* / *Data situation*, field-standard concerns already answered. Losses only from checklists/losses-hazards.md — never invented. L0 or any RULES §12 trigger → the Director asks D9. Draft hazards are unnumbered (h-n); the concept numbers them. -->

**System card**

| Line | Value | Evidence |
| --- | --- | --- |
| Autonomy | <suggests only | acts with human confirmation | acts alone> | <…> |
| Exposure | <internal | partner | public> | <…> |
| Learning loop | <no | yes — what feeds back> | <…> |
| Actions | <tools, write access, code execution, messages or requests to others, other agents — or "reads only"> | <…> |

**Losses**

| Loss | Relevant | What exactly would be lost, for whom | Prefilled from | Evidence |
| --- | --- | --- | --- | --- |
| L1 … L11 | <yes | not relevant — reason> | <…> | <GR-n | R-n | entry field | grilling> | <[evidenced] | [estimated]> |

- **Worst for this sponsor:** <one loss and one sentence>
- **Scope boundary:** <not relevant | trigger: … → D9 answer: …>

**Draft hazards**

| Draft | Hazard (system state) | Losses | Origin |
| --- | --- | --- | --- |
| h-1 | <…> | <L…> | <…> |

### New candidate requirements

| Draft ID | Requirement | Type | Origin | Evidence |
| --- | --- | --- | --- | --- |
| d-1 | <…> | F / U / NF | grilling #<n> | <…> |

### Still open after grilling

- **OQ-<n>** — <…> — <who can answer, by when>

## Part 3 — Draft for the concept stage

- **Path:** <A | B | mixed | R | S | A+R | B+R | mixed+R — and why>
- **Ubiquitous language seeds:** <terms from the brief's shared vocabulary plus new ones — goes to CONTEXT.md>
- **Out of scope (for now):** <…>
- **Integration points known:** <…>
- **Riskiest assumption after intake:** <from the brief, updated>
- **Recommended first vertical slice:** <one sentence — the smallest end-to-end path that proves the core>

## Notes (human)

<!-- Off limits for all skills. Copied verbatim on every rewrite. -->
