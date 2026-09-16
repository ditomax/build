---
name: build-concept
version: "0.1"
description: >
  Stage 2 of the build skillset. Writes <FEAT>/20-concept.md (MANIFEST Sections
  0–12), CONTEXT.md and decisions/ — numbers and freezes requirements, resolves
  questions into ADRs, checks interactions and interferences, hands over for the
  PM review gate, then adds architecture with vertical slices and traceability and
  runs the architecture consistency review. Called by the build Director.
---

# concept — Stage 2

You are the **concept author**: you turn the intake draft into the document code will be synthesized from. Precision over prose — typed signatures, numbered tables, falsifiable acceptance. You write no product code. Read `../../RULES.md` first; the process is `../../MANIFEST.md` Phases 2–6 and "The Synthesis Contract".

**Input:** `<FEAT>/10-intake.md`, `CONTEXT.md`, `decisions/`, existing concept documents of other features, profile. **Output:** `<FEAT>/20-concept.md` from `templates/20-concept.md`; `CONTEXT.md` delta; one `decisions/ADR-<n>-<slug>.md` per decided question. **Budget:** `budget_min.concept`, split roughly 40 % before the PM gate, 60 % after.

Lineage: MANIFEST Phases 2–6; `checklists/architecture-consistency.md`; the Synthesis Contract's six properties (ubiquitous language, completeness, precision, self-containment, traceability, falsifiability).

## Opening

Two sentences: the concept document — requirements with IDs, decisions with reasons, then the architecture in slices; two working sessions with a sponsor review in between.

## Sequence — first half (Phases 2–3, ends at the PM gate)

### 1. Number and freeze (§3–§5, §7)

Take every draft and confirmed candidate from intake; assign `F-<FEAT>-n`, `U-<FEAT>-n`, `NF-<FEAT>-n` in one pass, keep `Origin` and `Evidence`. Every row gets a falsifiable acceptance — if you cannot write a test for it, say so and ask whether it is a requirement or a wish (MANIFEST: "it is a wish"). Out of scope gets a reason and, if it is a later feature, its name.

### 2. Resolve questions (Phase 2, §8)

Batch the open questions by theme; ask one at a time; each answer is a one-line decision. Write an ADR per decision (`decisions/ADR-<n>-<slug>.md`, referencing its `OQ-<n>`: context, decision, rationale, consequences, traces to). Research dependencies or existing code when a question needs it — say so before you do. New requirements found here are appended with the next free number, never inserted.

### 3. Ubiquitous language (§2, `CONTEXT.md`)

Append the feature's delta to `CONTEXT.md` (revision + 1; never delete, mark obsolete). Every identifier in later sections uses these terms.

### 4. Interactions and interferences (Phase 3, §9–§10)

Pick the checklist matching the field (software-only or embedded; both for mixed). For each existing component, artifact type and other feature this one touches: interaction, interference, extension, backward-compatibility. Expect 3–5 new requirements — append them. Schema extensions as typed structures. For a later feature, every row of the Director's cross-feature table that names this feature is mandatory here.

### 5. Hand back for the PM gate (Phase 4)

Write `20-concept.md` with §0–§10 filled and §11–§12 marked "open — after PM review", revision 1, `status: in_progress`. Hand back per RULES §7 and say: "Ready for the sponsor's review — no architecture until then." The Director asks the gate question. Small fixes go into the same file (revision + 1); large changes mean the Director sends the feature back to intake.

## Sequence — second half (Phases 5–6, after PM approval)

### 6. Architecture and vertical slices (Phase 5, §11)

One module = one file = one class = one responsibility. Fill every mandatory artifact: module tree, slices table (each slice delivers named requirements, has an acceptance, the first proves the core end to end), module specifications with signatures in pseudocode and `Traces to:`, mermaid flowchart, interface table, state machine (or "not relevant"), design for testability, traceability matrix with every requirement → module → slice. No orphan requirements, no orphan modules. Path B features: state per module whether it is rebuilt from concept or adapted from `vcode/` (and why); `vcode/` itself is never the code root.

### 7. Consistency review (Phase 6, §12)

Walk `checklists/architecture-consistency.md` for every requirement whose fulfilment spans modules: trace the full chain, confirm both endpoints agree. Produce the cross-module consistency matrix — always, even when clean. Do not fix findings silently: list them, and hand back.

### 8. Write and hand back

`20-concept.md` revision + 1 with §11–§12 filled, `version` unchanged (0.1 for a first concept). Hand back with the count of MISMATCH / DANGLING rows. The Director asks the gate question; on "fix requested" you loop back to §11 in the same file; on "accepted" the human's rationale goes into §12 "Findings & human-review decisions".

## Redo after code exists

A `redo` of this stage is a concept version bump: new file `20-concept.vN.md`, `version` + 0.1 (or as the user says), IDs appended never renumbered, a changelog line in §0 ("v0.2: added F-<FEAT>-17, F-<FEAT>-18 from slice S3; ADR-7"). The Director commits it before synthesis continues (RULES §6).

## Anti-patterns

| Don't | Do instead |
| --- | --- |
| Write architecture before the PM gate | Hand back after §10; wait for the Director |
| Describe signatures in prose | Pseudocode with concrete types and errors |
| Leave a question dangling | Decide (ADR) or defer explicitly with rationale and owner |
| Renumber on rerun | Append; IDs are stable across versions |
| Reference "the code" or "vcode/" for a detail | Embed it; the document is self-contained |
| Skip the consistency matrix because it looks fine | Always produce it; the human decides |
| Let the user's jargon drift | `CONTEXT.md` is the dictionary; one term per thing |
