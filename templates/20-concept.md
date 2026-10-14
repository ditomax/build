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

_Sections 0–12 of the MANIFEST document structure. Together with `30-synthesis.md` (Sections 13–16) this is the source of truth for the feature's code: a fresh agent, given only these files and `CONTEXT.md`, must be able to regenerate the implementation. IDs carry the feature code (`F-<FEAT>-1`) and never change after `done`; reruns append, never renumber._

## 0. Synthesis Context

- **Development path:** <A: frontloaded | B: retrofitted from vcode/ | mixed | R / S (from idea) | A+R · B+R · mixed+R>
- **Synthesis dependencies:** <other feature concept documents this feature requires, with version — or "none">
- **Synthesis scope:** <what can be regenerated from this document alone; what needs the dependencies>
- **Field of application:** <one line — drives the checklists>

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

## 10. Schema Extensions

<typed structures — tables, messages, config keys — added or changed; each with type, default, valid range where applicable>

## 11. Architecture & Vertical Slices

<!-- Written only after the PM gate (Phase 4) is approved. -->

### Module overview & tree

```
<module tree — one module = one file = one class = one responsibility>
```

### Vertical slices

| Slice | Name | Delivers (requirements) | Depends on | Acceptance |
| --- | --- | --- | --- | --- |
| S1 | <…> | F-<FEAT>-1, U-<FEAT>-1 | — | <…> |

### Module specifications

<!-- Per module: responsibility, class and method signatures in pseudocode with concrete types, errors raised, `Traces to: F-<FEAT>-x, NF-<FEAT>-y`. -->

#### <module>

- **Responsibility:** <…>
- **Traces to:** <…>

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

### Design for testability

<seams, injection points, fakes for external services, deterministic clocks and IDs>

### Scaling strategy

<!-- From §5 Load and growth model and checklists/requirements.md § Capacity & Scaling. With "single instance is enough" this is still filled: the bottleneck we would hit first, and when. -->

- **Capacity budget:** <instances × pool size vs. database limit; workers; calls per minute vs. external limits>
- **First bottleneck:** <component> at <load, from §5> — **lever:** <larger instance | index or query fix | cache | queue | read replica | second instance> — **trigger:** <measured signal, e.g. p95 latency > … or pool wait > …>

### Requirement traceability matrix

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

## Notes (human)

<!-- Off limits for all skills. Copied verbatim on every rewrite. -->
