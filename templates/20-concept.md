---
stage: concept
owner: build-concept
status: in_progress          # open | in_progress | done | skipped
revision: 1
version: 0.1                 # concept document version — bumped at every release point (MANIFEST "Concept ↔ Code Synchronization")
created: <YYYY-MM-DDThh:mm>
updated: <YYYY-MM-DDThh:mm>
input: <FEAT>/10-intake.md@<rev>
feature: <FEAT>
---

# <FEAT> Concept — Consolidated Requirements

_A rule is defined once, in its owning table (requirement, SC, A, E, UCA); every other section — §8, §10, §11 included — cites its ID and adds only new context. Sections 0–12 of the MANIFEST document structure. Together with `30-synthesis.md` (Sections 13–16) this is the source of truth for the feature's code: a fresh agent, given only these files and `CONTEXT.md`, must be able to regenerate the implementation. IDs carry the feature code (`F-<FEAT>-1`) and never change after `done`; reruns append, never renumber._

## 0. Synthesis Context

- **Development path:** <A: frontloaded | B: retrofitted from vcode/ | mixed | R / S (from idea) | A+R · B+R · mixed+R>
- **Synthesis dependencies:** <other feature concept documents this feature requires, with version — or "none">
- **Synthesis scope:** <what can be regenerated from this document alone; what needs the dependencies>
- **Field of application:** <one line — drives the checklists>
- **Scope boundary:** <not relevant | warning acknowledged on <date>: <simulation / teaching | non-safety part only>; carved out: <…>> (RULES §12)
- **System card:** autonomy <…> · exposure <…> · learning loop <…> · actions (state effects) <…> · outputs (information effects) <… — used by <whom>, <checked by <role> against <what> | unchecked>>
- **Error handling depth:** <per outside action, from §9 *Outside actions* and §11 — e.g. full: credit note · output: reply mail, nightly report · short: ticket> (checklists/error-handling.md §3)

### Register trace

<!-- Path R only (idea register, directly or via brief H2/3); otherwise the line "not relevant — no register". One row per R-ID of 10-intake.md "Register trace". IDs here never change; reruns append. -->

| Item | Priority | Requirements | Verdict |
| --- | --- | --- | --- |
| <R-n | R-M<n>> | <MUST | SHOULD | COULD — R-M: confirmed at the PM gate> | <F-<FEAT>-n, NF-<FEAT>-m | —> | <mapped | feature <CODE> | → OQ-n | deferred: … | out: …> |

## 1. Purpose

<one paragraph, in the sponsor's words>

## 2. Ubiquitous Language & Context

<!-- Terms introduced or changed by this feature. The project-wide dictionary is CONTEXT.md; this section lists the delta and links there. -->

| Term | Meaning | Used in code as |
| --- | --- | --- |
| <…> | <…> | <identifier> |

## 3. Functional Requirements

| ID | Requirement | Acceptance (falsifiable) | Origin | Evidence |
| --- | --- | --- | --- | --- |
| F-<FEAT>-1 | <…> | <what a test observes> | <intake d-n / brief cF-n / R-n · d-n / entry <ID> · d-n / grilling> | <[evidenced] | [estimated]> |

## 4. User Interaction Requirements

| ID | Requirement | Acceptance | Origin | Evidence |
| --- | --- | --- | --- | --- |
| U-<FEAT>-1 | <…> | <…> | <…> | <…> |

## 5. Non-Functional Requirements

<!-- Includes every mandatory or regulatory standard for the field of application as its own numbered row. -->

| ID | Requirement | Acceptance / measure | Origin | Evidence |
| --- | --- | --- | --- | --- |
| NF-<FEAT>-1 | <…> | <…> | <checklist row / grilling> | <…> |

### Load and growth model

<!-- From 10-intake.md § Load and growth. The single source of volume figures: the operating cost model below and every capacity requirement take theirs from here. -->

| Quantity | At launch | 1 year | 3 years | Peak factor | Evidence |
| --- | --- | --- | --- | --- | --- |
| Users / concurrent at peak | <…> | <…> | <…> | <…> | <…> |
| Requests per second at peak | <…> | <…> | <…> | — | <…> |
| Data volume / largest table or collection | <…> | <…> | <…> | — | <…> |
| Calls to external services per minute vs. their limit | <…> | <…> | <…> | <…> | <…> |

- **Scale decision:** <single instance is enough — scale-out deferred, reason: the figures above | scale-out needed → NF-<FEAT>-n>

### Operating cost model

<!-- From 10-intake.md § Running cost and checklists/requirements.md § Operating Cost. "Fixed running cost only — no usage-dependent drivers" is valid one-line content. Every guard is an NF row above. -->

| Cost driver | Unit | Cost per unit | Units per typical action | Volume per month (expected / peak) | Cost per month (expected / peak) | Guard | Evidence |
| --- | --- | --- | --- | --- | --- | --- | --- |
| <AI model call> | <1k tokens> | <…> | <…> | <…> | <…> | <cache · quota · cap → NF-<FEAT>-n> | <[evidenced] | [estimated]> |

- **Cost per typical action:** <…>
- **Covered by:** <price per user | fixed budget | nobody yet> — expected load <yes | no>, heaviest user <yes | no → quota NF-<FEAT>-n>

### Losses, hazards and constraints

<!-- STPA-light, checklists/losses-hazards.md. From 10-intake.md § Losses and hazards. Losses: the selected L-IDs with one line each. Every hazard reaches at least one constraint. Every SC has two halves (build's convention): keep out, and the response where applicable — what must be detectable, the immediate action (class: hold · safe state · protective step · complete or compensate · at most once then a person · reduced service, checklists/losses-hazards.md §1a), the remedy and within which time — or "none viable — reason". The response is a requirement on the design: it becomes rows of the reaction matrix (§11 Error handling) and its remedy joins the remedies below — no constraint, no remedy. A testable SC is itself a requirement: its Acceptance is the test. The scenarios that could violate it are refined and answered after the architecture (§11). F/U/NF rows only for what an SC needs outside itself (their Origin names the SC). "Enforced by": code · configuration · prompt + test · prompt · human · organisation — on Path B and in rescue "now → target". An SC for a gate loss (checklists/losses-hazards.md §6: L1, L2, L3, L9–L12 and the sponsor's worst) enforced only by prompt, human or organisation is a finding for the PM gate. IDs never change; reruns append. -->

- **Losses:** <L-ID — what exactly, for whom; one per line | not relevant ones: L-ID — reason>
- **Worst for this sponsor:** <L-ID — from intake I11; a gate loss>
- **Loss events:** <per gate loss: the moment it occurs and the time to loss (e.g. L12 — the credit note is booked, at the night run; L2 — the reply leaves the relay, seconds); rows and SCs cite this line>

| H-ID | Hazard (system state) | Losses | Asset at stake | Origin |
| --- | --- | --- | --- | --- |
| H-<FEAT>-1 | <…> | <L…> | <…> | <h-n | GR-n | R-n | checklist | UCA-<FEAT>-n | E-<FEAT>-n (ignore test)> |

| SC-ID | Keep out | Response: detect · immediate action · remedy · within | Hazards | Enforced by | Acceptance |
| --- | --- | --- | --- | --- | --- |
| SC-<FEAT>-1 | <the rule that keeps the system out of the state> | <detect: <what, within which time, independent of what (faults covered, shared dependencies), against which reference> · immediate action: <hold | safe state: <state> | protective step: <move> | complete or compensate | at most once, then <person> | reduced service: <what continues>> · remedy: <M-<FEAT>-n> · within <time, before the loss event> | none viable at runtime — <reason>, limited by <transparency on the output · a value band> | n/a — the keep-out removes the hazard (revisit: <trigger>)> | H-<FEAT>-1 | <code | configuration | prompt + test | prompt | human | organisation> | <what a test observes | — (carried out by the reaction matrix, §11)> |

- **Bridge tests:** <per gate-loss hazard: the immediate action holds until the remedy has taken effect — remedy bound <time: detect + decide + effect, slowest realistic path> vs. time to loss <time>; for hold: the situation drifts to <loss> in <time> by itself; the immediate action itself causes <loss at <time> | nothing> → response stands | another class or a faster remedy | trade-off OQ-n | finding (bound unknown · fail-operational: capability or scope decision)>

- **Sponsor principle:** <one line the sponsor set for autonomy and who gets warnings (C18), dated | none>

**Remedies** — the responses' remedies merged, plus *classify and fix* (C10a).

| M-ID | Remedy | Who or what (role · deputy, or mechanism) | For | Triggered by | Time to remedy | Status |
| --- | --- | --- | --- | --- | --- | --- |
| M-<FEAT>-1 | <restart | software update | replace | reconfigure | correct data | renew access | contact outside party | decide a held item | manual fallback | failover | mask | safe default at the receiver | classify and fix — named for the product> | <…> | <SC-<FEAT>-n, … | unplanned errors> | <in words before the PM gate ("the export-age warning"); E-IDs added in §11> | <e.g. within 1 working day · on-call 17–22 · nights nobody · seconds (watchdog) — checked against the operating environment> | <proposed — to be confirmed by <role> | confirmed by <name, role>, <date> (source: C15/C10a)> |

- **Storage and retention:** <where warning and critical events are kept durably (C13a) and for how long (C13b)>

## 6. Integration Points

| System / module | Direction | Contract (schema, protocol, version) | Owner |
| --- | --- | --- | --- |
| <…> | in / out / both | <…> | <…> |

## 7. Out of Scope

- <…> — <reason, and whether it is a later feature>

## 8. Resolved Questions & ADRs

<!-- One row per decided question. A design decision gets an ADR: decisions/ADR-<FEAT>-<nnn>-<slug>.md (counter per feature). A question settled by an intake fact rather than a decision points to the requirement it produced (→ F-<FEAT>-n) instead. Original OQ numbers from the brief are kept; questions raised in build continue the sequence. -->

| OQ | Question | Decision | ADR |
| --- | --- | --- | --- |
| OQ-<n> | <…> | <one line> | <decisions/ADR-<FEAT>-<nnn>-<slug>.md | → F-<FEAT>-n> |

### Open questions

- **OQ-<n>** — <…> — <blocks: F-<FEAT>-x | nothing yet>

## 9. Artifact Interactions & Interferences

<!-- MANIFEST Phase 3, with the software-only or embedded checklist matching §0 "Field of application". One row per existing component, artifact type or other feature this feature touches. -->

| Existing component / feature | Interaction (how we use it) | Interference (how we might break it) | Extension needed | Backward-compatible |
| --- | --- | --- | --- | --- |
| <…> | <…> | <…> | <…> | yes / no / n.a. |

### Control structure

<!-- STPA-light, checklists/losses-hazards.md §3. Every controller that can act on the product: user roles, operators, system components, error handlers, AI components, external services, scheduled jobs, mechanical checks. The people and external mechanisms that carry out remedies (operator, maintenance, IT, data owner; watchdog, redundant component, voter) appear with their M-IDs as control actions; the people's feedback is what error handling indicates and stores. Every person named as a check on an output appears here with what they check against. The error handler appears once its immediate actions are known (§11 Error handling), one per level; its control actions are the A-IDs, its feedback the detectors. The repeaters of intake I13 and the product's own re-runs appear as controllers. Where people set a state the handler also sets (a pause), name both and who wins. Written before the PM gate at the level of roles and components; §11 refines it per module (Regulation line). -->

| Controller | Kind | Control actions | Controlled process | Feedback | Process model (what it believes, how refreshed) |
| --- | --- | --- | --- | --- | --- |
| <…> | <human user role | operator | system component | error handler | AI component | external service | scheduled job | mechanical> | <…> | <…> | <…> | <…> |

```mermaid
flowchart TD
  <controller> -->|<control action>| <controlled process>
  <controlled process> -.->|<feedback>| <controller>
```

**Outside actions** — every control action with an effect outside the product that passes the filter (state effect on a gate loss, or output used unchecked); same counterparty and commit boundary → one group (checklists/losses-hazards.md §3). Author's columns confirmed in one line by the sponsor and IT; C16 to IT; C17 to the sponsor only where it arises.

| Action (group) | Effect · losses · depth | Commit boundary; before · during · after | Counterparty (C16) | Repeaters | Repeat safety | Decisions (C17) |
| --- | --- | --- | --- | --- | --- | --- |
| <create credit note> | <state · L12 · full> | <ERP booked; before: nothing happened · during: unknown · after: booked, ledger not yet written> | <per answer: 201 = booked · 4xx = not booked · timeout, 502, vendor codes = unclear (code table, IT); listable by order no. after <window>; no key — IT, <date> | OQ-n> | <cron wrapper 3× — off for this job, not relied on · container restart and own re-poll of the unacknowledged mail → resumable · agent re-booking by hand → sees the reservation> | <idempotent | resumable | compensable | at most once, then <person>> | <none | SC-a vs SC-b → OQ-n (trade-off Lx↔Ly) · stop released by <role, through what> · completed by <person> after at most once> |

### Unsafe control actions

<!-- One row per control action that can lead to a hazard — outputs someone acts on included. Types: N not provided · P provided and harmful · T too early / too late / wrong order · D stopped too soon / applied too long. Causes are the coarse scenarios: CS1–CS7 (checklists/losses-hazards.md §5) with one concrete line each, AI threats as T01–T22 (checklists/ai-threats.md); they are refined and answered after the architecture (§11 Scenarios). Every UCA reaches an SC — new SCs go into §5 above and produce requirements with the next free number. Control actions without a path to a selected loss: one line below the table. Every UCA reaches an SC, or SC: — (OQ-n, trade-off Lx↔Ly) for the PM gate. The UCA check of the error handling follows in §11. -->

| UCA-ID | Controller | Control action | Type | Unsafe in this context | Hazard | Causes | SC |
| --- | --- | --- | --- | --- | --- | --- | --- |
| UCA-<FEAT>-1 | <…> | <…> | <N | P | T | D> | <…> | H-<FEAT>-n | <CS…: <concrete scenario> · T…> | SC-<FEAT>-n |

- **No path to a selected loss:** <control actions skipped, one line | none>

## 10. Schema Extensions

<typed structures — tables, messages, config keys — added or changed; each with type, default, valid range where applicable>

## 11. Architecture & Vertical Slices

<!-- Written only after the PM gate (Phase 4) is approved. -->

### Layout

<!-- RULES §15. First feature: the profile chosen with C8, recorded in the layout ADR. Later features: a pointer to that ADR and where this feature's unit sits. -->

- **Profile:** <py-lib | py-web | c-cpp | rust | ios | android | unreviewed:<name> | adopted> — `decisions/ADR-<FEAT>-<nnn>-layout.md`
- **Package / project name:** <…>
- **This feature's unit:** <e.g. `src/<pkg>/<feat>/`, crate `<pkg>-<feat>`, `:feature:<feat>`>
- **Depends on:** <core and the features it may use — the dependency rule the layout check enforces>
- **User CLI (C9):** <none | separate | one tool — ADR>

### Module overview & tree

```
<module tree inside the layout — one responsibility per module; many small files are fine (guideline L6)>
```

<!-- Path B only: one line per module — rebuilt from the concept | adapted from vcode/<file> (part used, why). -->

### Vertical slices

| Slice | Name | Delivers (requirements) | Depends on | Acceptance |
| --- | --- | --- | --- | --- |
| S1 | <…> | F-<FEAT>-1, U-<FEAT>-1 | — | <…> |

### Module specifications

<!-- Per module: responsibility, class and method signatures in pseudocode with concrete types, errors raised (per the profile's error convention, layouts/<profile>.md §8; each caught where its E row's action can be taken), `Traces to: F-<FEAT>-x, NF-<FEAT>-y`. -->

#### <module>

- **Responsibility:** <…>
- **Traces to:** <…>
- **Regulation:** <enforces SC-<FEAT>-n; watches <feedback>; on violation <E-<FEAT>-n, … | refuse | degrade | contain | alert>; refreshes its process model by <…> | not a controller>

```
<signatures>
```

### Pipeline flow

```mermaid
flowchart LR
  <…>
```

### Inter-module interfaces

| From | To | Call / message | Types | Errors |
| --- | --- | --- | --- | --- |
| <…> | <…> | <…> | <…> | <…> |

### State machine

```mermaid
stateDiagram-v2
  <… or "not relevant for this feature">
```

### Scenarios

<!-- STPA, refined after the architecture (C11): the coarse scenarios of §9 as causal paths per module and interface, each answered in the design order. Eliminate, prevent and limit become requirements; detect and control joins the constraints' responses as a requirement for the reaction matrix. -->

| Scenario (circumstances → what the controller got wrong or could not do) | Type | UCA / SC | Hazard | Answer | By | Reason · owner · verification |
| --- | --- | --- | --- | --- | --- | --- |
| <…> | <a: unsafe action issued or needed one omitted | b: safe action not carried out correctly> | UCA-<FEAT>-n · SC-<FEAT>-n | H-<FEAT>-n | <eliminate | prevent | detect and control | limit the damage | accept> | <F/U/NF-<FEAT>-n | E-<FEAT>-n | residual risk> | <why this answer; who owns it; the test, the row, or the acceptance (name, date); revisit trigger if it rests on an assumption> |

### Error handling

<!-- RULES §16, checklists/error-handling.md. After the architecture, decided at the consistency gate (§12). The four steps: (1) actions — the immediate actions below, the remedies from §5; (2) errors the design can detect with their root causes; (3) the reaction matrix; (4) indication and storage. Depth per outside action (§0, §9): output — wrong · missing · late, absence detector, value band; short — its rows and its standing row; full — everything. Matrix rows come from a constraint's requirement, a detectable error, or the standing row of an outside action for unexpected errors; every row names its source. Merge rows with the same handling before adding. An E row is a requirement: immediate action, bound and escalation are its acceptance. Living: a new scenario or error gets its answer through a concept redo before code handles it. -->

**1 — Immediate actions** — what the product does now, until the remedy (C10b).

| A-ID | Immediate action | Serves class (SC) | Level | What it does | Autonomy | Released by |
| --- | --- | --- | --- | --- | --- | --- |
| A-<FEAT>-1 | <log and continue | skip | retry | correct from the authoritative source | cancel own action | hold before effect | protective step | reduced service | safe state | report to <role, channel> — named for the product> | <hold — SC-<FEAT>-n> | <item | run | component | service | system> | <…> | <automatic | automatic with warning | operator only — by the sponsor, the principle, or delegated to <role>, dated> | <role, through what | —> |

**2 — Errors** — what the design can detect, by category, with root causes (C11).

| Error (category) | Detected by · reference | Root causes | Scenario | Row |
| --- | --- | --- | --- | --- |
| <…> | <detector, when it looks · reference> | <cause; …> | <scenario · H-<FEAT>-n | none → ignore test> | <E-<FEAT>-n> |

**3 — Reaction matrix** (C12).

| E-ID | Error | Detected by · reference | Immediate action | Bound → then | Remedy | Log | Source |
| --- | --- | --- | --- | --- | --- | --- | --- |
| E-<FEAT>-1 | <category; merged states separated by ·> | <detector, when it looks · reference of SC-<FEAT>-n> | <A-<FEAT>-n | A-<FEAT>-n → A-<FEAT>-m> | <n cycles of the late thing | n attempts by <deadline> | <calendar time> — (≈ wall-clock; time to loss <…>) → A-<FEAT>-m | until <remedy or person>> | <M-<FEAT>-n | none needed> | <counter | warning | critical> | <SC-<FEAT>-n (requirement) | scenario · UCA-<FEAT>-n · H-<FEAT>-n | operational: F/NF-<FEAT>-n> |
| E-<FEAT>-n | unexpected error at <outside action> | <the action-boundary helper> | <before: <class of the SC> · during (outcome unknown): reconcile | at most once, then <person> · after: complete the own record> | <…> | <M-<FEAT>-n> | critical | unexpected at <action> — dead process: <external mechanism | finding> · repeaters: <repeat-safe how> |
| E-<FEAT>-H | harmless (merged): <states> | <…> | log and continue | <per source: n per <period> above baseline> → report | none — review when frequent | counter | ignore test: <per entry: reason · revisit trigger> |
| E-<FEAT>-n | <value | outcome rate> outside band: <figure or countable effect per period> | <against a band set by <person | second source>> | <hold | report> | <…> | <…> | critical | <SC-<FEAT>-n · H-<FEAT>-n — detector of last resort> |

**4 — Indication and storage** (C13c): <fallback while the store is down; where a safe state lives so it survives a restart; what an operator sees; what the remedy owner needs to diagnose — item, step, input, version, since when>

- **UCA check of the error handling:** <per immediate action, remedy and repeater: N · P · T · D found → written into the row or a new SC / hazard / row | checked, nothing found>
- **Independence:** <per detector, escalation channel, watchdog and stored safe state: what it depends on, and the second channel or outside controller that does not fail with it | checked, nothing shared>
- **Feedback to the remedy owners:** <per remedy: what the person needs to know, what they receive, in time and understandable? | checked>
- **Classification:** <errors classified by the ignore test — missing scenario (→ row) · missing hazard (→ H, SC with response) · operational requirement · harmless (→ E-<FEAT>-H) · reaches a loss by designer decision (ADR-…, revisit trigger) | none>
- **Further requirements:** <F/U/NF-<FEAT>-n for what lies outside a row (screen, store, setting) — Origin: E-<FEAT>-m | none>

### Design for testability

<seams, injection points, fakes for external services, deterministic clocks and IDs — every E row with a time bound needs a clock seam and a `run_once(now)` per job, every E row on an external call a fake that can fail the way the row says; every outside action a way to raise an unexpected exception before, during and after its commit boundary, to kill the process mid-action, to restart it and to fire each repeater>

### Scaling strategy

<!-- From §5 Load and growth model and checklists/requirements.md § Capacity & Scaling. With "single instance is enough" this is still filled: the bottleneck we would hit first, and when. -->

- **Capacity budget:** <instances × pool size vs. database limit; workers; calls per minute vs. external limits>
- **First bottleneck:** <component> at <load, from §5> — **lever:** <larger instance | index or query fix | cache | queue | read replica | second instance> — **trigger:** <measured signal, e.g. p95 latency > … or pool wait > …>

### Requirement traceability matrix

<!-- Every requirement, E rows included (an E row is a requirement). -->

| Requirement | Module(s) | Slice | Test (from 30-synthesis.md) |
| --- | --- | --- | --- |
| F-<FEAT>-1 | <…> | S1 | <filled by synthesis> |

## 12. Architecture Consistency Review

<!-- MANIFEST Phase 6 with checklists/architecture-consistency.md. Always run, always produce the matrix; the human decides at the gate. -->

### Cross-module consistency matrix

| Requirement | Element | Chain (source → … → destination) | Modules | Verdict | Note |
| --- | --- | --- | --- | --- | --- |
| <…> | <…> | <…> | <…> | OK / MISMATCH / DANGLING | <…> |

### Findings & human-review decisions

| Finding | Decision | By | Date | Rationale |
| --- | --- | --- | --- | --- |
| <…> | accepted / fixed / deferred | <…> | <…> | <…> |

> Produced with the skill suite (guidelines only) — review and responsibility lie with the operator; see `DISCLAIMER.md`.

## Notes (human)

<!-- Off limits for all skills. Copied verbatim on every rewrite. -->
