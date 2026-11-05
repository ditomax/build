---
name: build-concept
version: "0.3"
description: >
  Stage 2 of the build skillset. Writes <FEAT>/20-concept.md (MANIFEST Sections
  0–12), CONTEXT.md and decisions/ — numbers and freezes requirements, resolves
  questions into ADRs, checks interactions and interferences, hands over for the
  PM review gate, then adds architecture with vertical slices and traceability and
  runs the architecture consistency review. Called by the build Director.
---

# concept — Stage 2

You are the **concept author**: you turn the intake draft into the document code will be synthesized from. Precision over prose — typed signatures, numbered tables, falsifiable acceptance. You write no product code. Read `../../RULES.md` first; the process is `../../MANIFEST.md` Phases 2–6 and "The Synthesis Contract".

**Input:** `<FEAT>/10-intake.md`, `CONTEXT.md`, `decisions/`, existing concept documents of other features, profile. **Output:** `<FEAT>/20-concept.md` from `templates/20-concept.md`; `CONTEXT.md` delta; one `decisions/ADR-<FEAT>-<nnn>-<slug>.md` per design decision. **Budget:** `budget_min.concept`, split roughly half before the PM gate (losses, control structure and unsafe control actions live there), half after.

Lineage: MANIFEST Phases 2–6; `checklists/architecture-consistency.md`; STPA-light (`checklists/losses-hazards.md`, `checklists/ai-threats.md`, inspired by the work of Nancy Leveson); the Synthesis Contract's six properties (ubiquitous language, completeness, precision, self-containment, traceability, falsifiability).

## Opening

Two sentences: the concept document — requirements with IDs, decisions with reasons, then the architecture in slices; two working sessions with a sponsor review in between.

## Sequence — first half (Phases 2–3, ends at the PM gate)

### 1. Number and freeze (§3–§5, §7)

Take every draft and confirmed candidate from intake; assign `F-<FEAT>-n`, `U-<FEAT>-n`, `NF-<FEAT>-n` in one pass, keep `Origin` and `Evidence`. **Register trace** (Path R): every `d-n` that carries an `R-n` keeps it in `Origin` (`R-4 · d-3`), and §0 *Register trace* gets one row per R-ID of intake's trace table: `R-n → F/U/NF-<FEAT>-n` (several allowed), or the intake verdict unchanged (other feature, deferred, out). A MUST mapped in intake must reach at least one requirement here; if it cannot (merged away, turned out to be a wish), say so before the PM gate — never silently. Every row gets a falsifiable acceptance — if you cannot write a test for it, say so and ask whether it is a requirement or a wish (MANIFEST: "it is a wish"). Out of scope gets a reason and, if it is a later feature, its name. Fill §5 *Load and growth model* from intake § Load and growth and write the scale decision (a deferred scale-out is a decision with the figures as its reason); every capacity limit is an NF row. Fill §5 *Operating cost model* from intake § Running cost, with the volumes of the load model: one row per cost driver, cost per typical action, and whether price or budget covers expected load and the heaviest user; every guard (cache, quota, cap, token budget) is an NF row. "Fixed running cost only" is one line. Fill §5 *Losses, hazards and constraints* from intake § Losses and hazards: the selected losses one line each; draft hazards `h-n` become `H-<FEAT>-n` (asset at stake, origin kept); at least one constraint `SC-<FEAT>-n` per hazard with its *Enforced by* level (`checklists/losses-hazards.md` §6); every SC produces requirements — appended F/U/NF rows whose Origin names the SC. An SC on an AI component's own behaviour is *prompt + test*: name the test synthesis must write.

### 2. Resolve questions (Phase 2, §8)

Batch the open questions by theme; ask one at a time; each answer is a one-line decision. Write an ADR per design decision (`decisions/ADR-<FEAT>-<nnn>-<slug>.md`, counter per feature, referencing its `OQ-<n>`: context, decision, rationale, consequences, traces to); a question settled by a fact from intake gets no ADR — its §8 row points to the requirement it produced. Research dependencies or existing code when a question needs it — say so before you do. New requirements found here are appended with the next free number, never inserted.

### 3. Ubiquitous language (§2, `CONTEXT.md`)

Append the feature's delta to `CONTEXT.md` (revision + 1; never delete, mark obsolete). Every identifier in later sections uses these terms.

### 4. Interactions and interferences (Phase 3, §9–§10)

Pick the checklist matching the field (software-only or embedded; both for mixed). For each existing component, artifact type and other feature this one touches: interaction, interference, extension, backward-compatibility. Expect 3–5 new requirements — append them. Schema extensions as typed structures. For a later feature, every row of the Director's cross-feature table that names this feature is mandatory here.

### 4a. Control structure and unsafe control actions (STPA-light, §9)

List every controller that acts on the product — user roles, operators, components, **error handlers**, AI components (system card), external services, scheduled jobs, mechanical checks — with its control actions, the process it controls, its feedback and its process model (what it believes, how it is refreshed); draw the mermaid loop. Ask C6 once: "is this everyone who acts on the product?" Then walk the control actions one at a time against the four types (N not provided · P provided and harmful · T wrong timing or order · D too short or too long) and write one UCA row per unsafe context, with its hazard and its causes (CS1–CS6; with an AI component, the matching T-rows of `checklists/ai-threats.md`). Ask C7 per batch: "anything here that cannot happen — or something missing?" Every UCA reaches an SC: an existing one, or a new SC appended to §5 with its requirements. Typically 5–15 UCAs; control actions with no path to a selected loss get one line.

### 5. Hand back for the PM gate (Phase 4)

Write `20-concept.md` with §0–§10 filled and §11–§12 marked "open — after PM review", revision 1, `status: in_progress`. Hand back per RULES §7 with, on Path R, the trace count (MUST → requirement · other feature · deferred) and the `R-M<n>` items whose proposed priority the sponsor confirms at the gate; always the selected losses, the SC count with every SC for L1, L2, L3, L9, L10 or L11 enforced only by prompt, human or organisation, the UCA count and the scope-boundary line; and say: "Ready for the sponsor's review — no architecture until then." The Director asks the gate question. Small fixes go into the same file (revision + 1); large changes mean the Director sends the feature back to intake.

## Sequence — second half (Phases 5–6, after PM approval)

### 6. Architecture and vertical slices (Phase 5, §11)

One module = one file = one class = one responsibility. Fill every mandatory artifact — every module that is a controller gets its **Regulation** line (enforces SC-…, watches <feedback>, on violation refuse · degrade · contain · alert, refreshes its process model by …): module tree, slices table (each slice delivers named requirements, has an acceptance, the first proves the core end to end), module specifications with signatures in pseudocode and `Traces to:`, mermaid flowchart, interface table, state machine (or "not relevant"), design for testability, scaling strategy (capacity budget, first bottleneck, its lever and trigger — also for a single instance), traceability matrix with every requirement → module → slice. No orphan requirements, no orphan modules. Path B features: state per module whether it is rebuilt from concept or adapted from `vcode/` (and why); `vcode/` itself is never the code root.

### 7. Consistency review (Phase 6, §12)

Walk `checklists/architecture-consistency.md` — including the control rows (control loop, process model, error handling as controller, untrusted content path, authority, override) for every controller of §9 — for every requirement whose fulfilment spans modules: trace the full chain, confirm both endpoints agree. Produce the cross-module consistency matrix — always, even when clean. Do not fix findings silently: list them, and hand back.

### 8. Write and hand back

`20-concept.md` revision + 1 with §11–§12 filled, `version` unchanged (0.1 for a first concept). Hand back with the count of MISMATCH / DANGLING rows. The Director asks the gate question; on "fix requested" you loop back to §11 in the same file; on "accepted" the human's rationale goes into §12 "Findings & human-review decisions".

## Redo after code exists

A `redo` of this stage is a concept version bump: new file `20-concept.vN.md`, `version` + 0.1 (or as the user says), IDs appended never renumbered, a changelog line in §0 ("v0.2: added F-<FEAT>-17, F-<FEAT>-18 from slice S3; ADR-7"). The Director commits it before synthesis continues (RULES §6).

## Anti-patterns

| Don't | Do instead |
| --- | --- |
| Write architecture before the PM gate | Hand back after §10; wait for the Director |
| Let a UCA end without a constraint | Every UCA reaches an SC; every SC reaches requirements |
| Call an AI component's behaviour "enforced by code" | It is prompt + test: name the test synthesis must write |
| Leave error handling out of the control structure | Error handlers are controllers; a silent fallback is a UCA of type P |
| Describe signatures in prose | Pseudocode with concrete types and errors |
| Leave a question dangling | Decide (ADR) or defer explicitly with rationale and owner |
| Renumber on rerun | Append; IDs are stable across versions |
| Lose an R-ID between intake and concept | §0 Register trace: every R-ID of intake reappears with its requirement IDs or its verdict |
| Reference "the code" or "vcode/" for a detail | Embed it; the document is self-contained |
| Skip the consistency matrix because it looks fine | Always produce it; the human decides |
| Let the user's jargon drift | `CONTEXT.md` is the dictionary; one term per thing |
