<!-- build skillset — normative reference. This is the Concept Document Authoring Manifest v8 (DMBG, 2026), carried into the build skillset unchanged in substance. The stage skills cite it as "MANIFEST Phase n". Path references were adjusted to this folder: checklists live in checklists/, the day-to-day rules of the former CLAUDE.local.md are in RULES.md §10, and the document structure template is realised as templates/20-concept.md + templates/30-synthesis.md. -->

# Concept Document Authoring Manifest

**Version 8**

**How the project builds software from concept documents — and why.**

---

## Why This Document Exists

The project builds software AI-first. That is not a slogan — it is an architectural decision with a concrete consequence:

**The concept documents are the source of truth. The code is a derivative.**

In a traditional software company, documentation describes what was built. In this project, documentation prescribes what will be synthesized. The markdown files in `documentation/` are not commentary — they are the specification from which an AI agent can regenerate the complete software stack: modules, interfaces, tests, and deployment configuration.

This inverts the usual hierarchy:

| Traditional | AI-First Project |
| --- | --- |
| Code is the source of truth | Concept documents are the source of truth |
| Docs describe the code | Docs prescribe the code |
| Tests verify the implementation | Tests verify the synthesis |
| Refactoring = changing code | Refactoring = changing the concept, then re-synthesizing |
| Knowledge lives in developers' heads | Knowledge lives in versioned markdown |

The practical consequence: if a concept document is incomplete, ambiguous, or wrong, the synthesized code will be incomplete, ambiguous, or wrong. There is no developer who "knows what was meant." The document must be self-contained.

This manifest defines how to write documents that meet that standard.

---

## The Two Paths

There are exactly two ways to arrive at a production-grade concept document. Both are valid. The choice depends on how well the problem is understood before work begins.

### Path A — Frontloading (Concept → Code)

Write the concept document first. Then synthesize the implementation from it.

* **When to use**: The domain is understood. Requirements can be stated before coding. The feature interacts with existing architecture in non-trivial ways.
* **Process**: Follow the 8 Phases below, in order. The concept document is complete before a single line of production code is written. Synthesis is a mechanical step.
* **Advantage**: Fewer false starts. Architecture is clean because it was designed, not discovered. Traceability is built in from the start.
* **Risk**: Over-engineering. Spending weeks on a concept for a feature that could have been prototyped in a day.

### Path B — Retrofit (Code → Concept)

Build first (vibe-code, prototype, spike). Then extract the concept document from the working code.

* **When to use**: The problem is novel. The right abstraction is unclear. You need to touch the code to understand the shape of the solution. Exploratory work, R&D, integrations with unknown APIs.
* **Process**:
1. Build a working prototype. Move fast. Ignore structure.
2. Once the prototype works, stop coding.
3. Extract the concept document from the code: requirements (reverse-engineered), architecture (as-built), and test concept (from what you learned).
4. Review the extracted concept through Phases 1–8 as if it were a draft.
5. Refactor the code to match the cleaned-up concept — or re-synthesize from scratch.


* **Advantage**: Fast feedback. You learn what you don't know by building.
* **Risk**: The retrofit never happens. The prototype becomes production code. Documentation is "planned for later" and never written. This is the single most common failure mode. **A prototype without a concept document is technical debt with no repayment plan.**

### The Rule

> Every module that ships to production must have a concept document, regardless of which path produced it. Path A produces better documents. Path B produces faster prototypes. Neither is optional — the question is only the sequence.

---

## The Synthesis Contract

The goal of every concept document is **synthesis-readiness**: an AI agent, given only the markdown files in `documentation/`, must be able to regenerate the complete implementation of that feature.

A concept document is synthesis-ready when it satisfies all of the following:

### Ubiquitous Language & Context

* A shared domain dictionary (e.g., `CONTEXT.md`) is maintained to ensure that developers and agents use the same jargon, derived from the same domain model.
* Variables, functions, and files are named consistently according to this shared language, reducing AI verbosity and saving thinking tokens.

### Completeness

* Every functional requirement has a corresponding module, method signature, and test case.
* Every interface between modules is specified (inputs, outputs, types, error handling).
* Every external dependency is named with version constraints.
* Every configuration parameter is listed with type, default, and valid range.
* Every deployment/support script is documented with purpose, invocation, target environment (dev/stage/prod), and required secrets/env vars.

### Precision

* Method signatures use concrete types, not prose descriptions.
* State machines have explicit states, transitions, and guards.
* Data schemas are specified as typed structures, not natural language.
* "Smart" behavior (LLM calls, heuristics) has explicit fallback logic.

### Self-Containment

* No requirement is explained by "see the code" or "ask [person]."
* Domain knowledge that a developer would need is embedded in the document, not assumed.
* Resolved questions include the reasoning, not just the decision.

### Traceability

* Every requirement maps to at least one module and one test.
* Every module traces back to at least one requirement.
* No orphan requirements. No orphan modules.

### Falsifiability

* Every requirement can be tested. If you cannot write a test for it, it is not a requirement — it is a wish.
* The test concept is specific enough that a second person (or agent) could implement the tests from the description alone.

> **Litmus test**: Hand the concept document to an AI agent that has never seen the codebase. If it cannot produce a working implementation that passes the test suite, the document is not ready.

---

## The 10 Phases

### Phase 1 — Critique the Draft via AI "Grilling"

Start with whatever exists (loose notes, bullet points, a half-finished doc). To avoid the common failure mode of misalignment, force the agent to rigorously interview you.

* **Prompt pattern**: *"Initiate a 'grilling session'. Review the requirements and relentlessly interview me to challenge the plan against our existing domain model. Point out missing information, extrapolate implementation implications, and extract open questions until every branch of our decision tree is resolved. Also infer the field of application (e.g. web app, embedded system, hardware system, lab equipment) from what I've given you, then hold our requirements against the state-of-the-art expectations for that field and call out every standard concern that is missing or undefined."*
* **Rules**:
* Requirements first, solutions later. Push back if the AI jumps to code.
* Separate functional, non-functional, and user interaction requirements.
* Number every requirement (`F-1`, `NF-1`, `U-1`). You will need the IDs later.
* Capture all open questions as numbered items (`Q1`, `Q2`, ...).
* **Infer the field, then check for completeness against it.** From the given information, estimate the field of application (web app, embedded system, hardware system, lab equipment, ...). Take that assumed field and verify that the state-of-the-art requirements for it are all actually defined — not assumed. For example, a web app is expected to address security, deployment, framework choice, credentials/secrets, usability, accessibility, and UI/UX; an embedded system, real-time behavior, resource limits, power, and safety. Surface every field-standard concern the draft leaves open as a new requirement or open question.
* **Mandatory and regulatory standards are requirements.** For the inferred field, actively query which standards are required or legally mandatory for state-of-the-art compliance (e.g. for a web app: GDPR/data protection, WCAG accessibility, OWASP security; for embedded/hardware: functional-safety and EMC standards like IEC 61508 / ISO 26262 / IEC 62304, CE marking). Do not treat them as background assumptions — name each one and document it as an explicit, numbered requirement.
* Record the inferred field explicitly as a one-line **`Field of application:`** entry near the top of the requirements, so later phases can reference it (Phase 3 uses it to choose the software-only vs. embedded checklist).



### Phase 2 — Resolve Open Questions & Sharpen Terminology

Answer the open questions from Phase 1. Do not do it all at once — batch by theme. Use this phase to document hard-to-explain decisions and build your shared language.

* **Prompt pattern**: *"Regarding the open questions: [answer Q1–Q5]. Now please research [X] and read [these docs]. Update our ubiquitous language in CONTEXT.md and write any necessary ADRs inline."*
* **Rules**:
* Provide crisp decisions, not discussions. Each Q gets a one-liner.
* Let the AI research dependencies (libraries, existing code) where needed.
* Newly discovered requirements get appended (`F-45`, `F-46`, ...).
* Move resolved questions into a "Resolved Questions" table.



### Phase 3 — Check System Interactions & Interferences

Before architecture, scan the existing system for interactions and interferences with the new requirements. **First decide what kind of target system you are building** — *software-only* or *embedded* — because the dimensions to check differ.

* **Prompt pattern (software-only)**: *"Read [artifacts.md, profiles.md, projects.md]. Look for interactions or interferences of our existing software with our new requirements, covering: module interfaces, data flows, control flows, resource usage, concurrency, deadlocks, synchronization, shared/global mutable state, race conditions, API/contract versioning and backward-compatibility, data-schema migration, error/exception propagation, transaction boundaries and atomicity, event ordering and sequencing assumptions, caching and cache invalidation, timeouts/retries/idempotency, reentrancy, resource leaks (handles, sockets, connections), pool exhaustion (thread/connection), blocking vs. async, starvation and priority, security boundaries (authn/authz), configuration coupling, dependency/version conflicts, and namespace collisions."*
* **Prompt pattern (embedded)**: *"Read [the relevant design and platform docs]. Look for interactions or interferences of the existing system with our new requirements, covering: hardware, interfaces, resource use, memory usage, cpu usage, concurrency, hardware versions, os, real-time deadlines and WCET, interrupt latency and ISR conflicts, priority inversion, jitter, bus contention (I2C/SPI/CAN), DMA and peripheral sharing, power modes and brown-out, clock/timing/synchronization, flash/EEPROM wear and write-cycle limits, stack overflow (limited stack), watchdog timeouts, firmware/bootloader/OTA-update compatibility, sensor/actuator calibration drift, EMI/electrical noise, temperature and environmental conditions, communication protocol versions, endianness, fixed-/floating-point limits, RTOS scheduling, heap fragmentation, buffer overruns, silicon errata and hardware revisions, pin-muxing conflicts, voltage/signal-level mismatches, and safety/fault-tolerance (redundancy, fail-safe)."*
* **Rules**:
* Pick the checklist that matches your target system (use both if it is a mixed hardware/software product), then walk every dimension that touches your feature.
* For each existing component or artifact type that touches your feature, document: **Interaction** (how your feature uses it), **Interference** (how your feature might break or conflict with it), and **Schema/design extensions** (what needs to change, ensuring backward-compatibility).
* This phase typically discovers 3–5 new requirements.



### Phase 4 — PM Review Gate

**Stop. Have the product manager read the document.** The document at this point contains: requirements (numbered), resolved questions, open questions, artifact interactions, and schema extensions. No architecture yet.

* **Rules**:
* Do not proceed to architecture without PM sign-off on requirements.
* PM feedback may add, remove, or reprioritize requirements.
* Small fixes go directly into the doc. Large changes restart Phase 1.



### Phase 5 — Architecture & Vertical Slices

Add the architecture chapter with full traceability, ensuring the design is explicitly broken down into small, independently grabbable tasks.

* **Prompt pattern**: *"Add an architecture chapter: modules with classes and methods, flowchart, interfaces, state machine, design for testability. Then, break this architecture down into independently-grabbable 'vertical slices'. Add traceability marks — map all requirements."*
* **Rules**:
* One module = one file = one class = one responsibility.
* Every module lists which requirements it fulfills (`Traces to: F-x, NF-y`).
* **Mandatory Artifacts**: Include the module tree, class/method signatures (pseudocode), a mermaid flowchart, an inter-module interface table, and a state machine diagram.
* End with a traceability matrix: every F/NF/U requirement $\rightarrow$ module(s). No orphan requirements.



### Phase 6 — Architecture Consistency Review (Advisory Gate)

After the architecture exists but before writing tests, check that it is internally consistent across modules. A single requirement is usually fulfilled by a chain of modules; this review traces each chain end-to-end.

* **Prompt pattern**: *"Read the architecture chapter and the [Architecture Consistency Checklist](./checklists/architecture-consistency.md). For every requirement whose fulfillment spans multiple modules, trace each architectural element end-to-end and confirm both endpoints agree and nothing dangles. Produce the Cross-Module Consistency Matrix and flag every MISMATCH or DANGLING."*
* **Rules**:
* For each cross-cutting requirement, walk every applicable element type in the checklist (data elements, functions, mechanisms, memory/storage, control signals, interfaces, state machines, end-to-end mechanisms, component life cycle, ...).
* Validate the complete chain, not the modules in isolation: source → transport → destination, producer → consumer, writer → reader, emitter → receivers, boundary-in → steps → boundary-out.
* Output the **Cross-Module Consistency Matrix**: one row per (requirement × element), with a verdict (OK / MISMATCH / DANGLING).
* **Advisory gate with human review**: the review always runs and always produces the matrix, but does not hard-block. On clean results, proceed. On any finding, surface it prominently and offer a human review — the human accepts the risk (with logged rationale), requests a fix (loop back to Phase 5), or defers specific items. The gate advises; the human decides.



### Phase 7 — Test Concept & TDD Contract

Add the test concept as the final documentation chapter. Ensure the tests are structured to support AI code generation via automated feedback loops.

* **Prompt pattern**: *"Add a test concept for unit tests and E2E flows. Structure the test definitions so that an AI agent can easily execute a red-green-refactor loop, building features one vertical slice at a time."*
* **Rules**:
* Unit tests per module with test case tables (input $\rightarrow$ expected $\rightarrow$ requirement).
* Integration tests must follow the full pipeline (E2E), using synthetic documents.
* **CI tests constraint**: No LLM calls, no network, all dependencies mocked. Must be fast and runnable before every deployment.
* **Synthetic test data strategy**: Use a template + variation approach, maintain golden outputs, and keep them versioned in the repository.
* **Coverage matrix**: Every requirement $\rightarrow$ at least one test. Explicitly justify any untested requirements.
* **Test & debug tooling register**: create `test_tooling.md` (companion file, next to `deployment.md`) as soon as test planning starts — even if it only holds the table header. It is the single inventory of every test runner, fixture generator, debug helper, inspection script, and diagnostic command the project has. One row per tool: name, purpose, invocation (exact copy-paste command line), key parameters, **environment & preconditions** (dev/stage/prod, needs LLM/network?, required mocks, test data location, env vars), link to detailed docs if any, status (active / obsolete). Reference it from the concept's Section 13 and from `CLAUDE.md` so every agent session loads it.



### Phase 8 — Synthesize Code via TDD Loop

Do not let the AI build everything at once. Feedback loops are the AI's speed limit.

* **Process**: Instruct the agent to implement the code in small, deliberate vertical slices using a strict red-green-refactor workflow.
* **Rules**:
* The agent must write a failing test first.
* The agent then writes the minimal code required to pass that test.
* Repeat the cycle sequentially until the vertical slice is completely fulfilled.
* Any deployment/support script generated along the way (setup, seed/migrate, deploy, rollback, smoke-check for dev/stage/prod) is documented immediately — in the concept's "Deployment & Operational Scripts" section or in a companion `deployment.md` — before the slice is considered done. An undocumented script is the same class of defect as undocumented code.
* **Before** writing any test, fixture, debug, or diagnostic script, the agent reads `test_tooling.md` and reuses an existing tool if one fits. Any new or changed tool is registered there in the same commit — before the slice is considered done. When a tool is removed, its row is removed (or marked obsolete with a one-line reason) in the same commit. An unregistered tool is the same class of defect as an undocumented script: the next session will reinvent it.



### Phase 9 — Synthesis Gap Analysis (Post-Synthesis Review Gate)

After the TDD loop has produced code for all vertical slices, check that what was actually implemented matches what the concept document specifies — not just that tests pass. The failure mode this phase targets: an agent that quietly drops a requirement, half-implements a module, or replaces real logic with a stub/mock/hardcoded return that happens to satisfy the test as written.

* **Prompt pattern**: *"Read the concept document's requirements (Section 3–5), architecture (Section 11), test concept (Section 13), and `test_tooling.md`. For every requirement, module, and test, open the actual implementation and confirm it does what the spec says — not a stub, TODO, hardcoded/mocked return, or partial case. Produce a Gap Analysis Matrix and flag every PARTIAL, MOCKED, or MISSING item."*
* **Rules**:
* Walk the traceability matrix (Section 11) requirement by requirement. For each F-x/NF-x/U-x, open the module(s) it traces to and read the real implementation — do not infer correctness from a module name, a docstring, or a passing test alone.
* Walk every module in the architecture chapter. Confirm every specified method/class exists and is implemented, not a stub, `pass`, `NotImplementedError`, or a function returning a fixed value regardless of input.
* Walk every test in the test concept (Section 13). Confirm it exercises the real code path rather than a mock so heavy it can no longer fail. A test passing against a stub is worse than no test — it hides the gap.
* Output the **Gap Analysis Matrix**: one row per (requirement / module / test), verdict — IMPLEMENTED / PARTIAL / MOCKED / MISSING — with a note on what's missing.
* Walk `test_tooling.md` in both directions: every registered tool still exists and runs as documented; every test/debug/diagnostic script in the repository is registered. Report orphans (unregistered scripts) and ghosts (registered but missing) as findings in the Gap Analysis Matrix; obsolete rows are pruned here at the latest.
* **Gate policy — advisory with human review** (same pattern as Phase 6): the review always runs and always produces the matrix. On a clean matrix, proceed. Any PARTIAL/MOCKED/MISSING finding is surfaced prominently; the human accepts the gap (logged rationale), sends it back to Phase 8, or defers with a tracked follow-up.
* A feature does not satisfy the Definition of Done (RULES.md §11) until its Gap Analysis Matrix is clean or every open item has a logged decision.
* This runs once, after synthesis is complete — not per vertical slice. Running it mid-flow would slow down the TDD loop; the end-gate catches the same gaps at feature-completion cost instead of per-slice overhead.



### Phase 10 — Architectural Rescue

Because AI-driven development drastically accelerates software entropy, complex applications can quickly degrade into a chaotic architectural state.

* **Process**: Periodically initiate a maintenance phase to rescue, refactor, and refine the health of the codebase.
* **Prompt Pattern**: *"Scan the codebase, CONTEXT.md, and docs/adr/. Find deepening opportunities to simplify interfaces and improve our codebase architecture."*

---

## Concept ↔ Code Synchronization

The concept document is not a throwaway artifact that goes stale after kickoff. It is the source of truth and must stay synchronized with the code for the entire life of the feature. Drift between concept and code is treated as a defect, not as normal aging.

We enforce synchronization at these lifecycle points:

* **Architecture defined (Phase 5):** the design in the concept must reflect the modules, interfaces, and slices actually planned — no design exists only in someone's head.
* **Every commit / PR:** code that adds or changes behavior cites the requirement IDs it touches (`Traces to: F-x, NF-y`). If the change has no matching requirement, the concept is updated in the *same* PR before merge — updating the doc is part of the definition of done, not a follow-up task.
* **Requirement discovered during implementation:** any new requirement found while coding is appended back to the concept (`F-46`, ...) immediately, with its test concept, before the code that implements it is merged.
* **Refactors:** requirement and module IDs stay stable across refactors so traceability is never broken; if a module is split or merged, its `Traces to:` marks move with it.
* **Review gates:** every review verifies traceability still holds in both directions — no orphan requirements (in the doc but not in code/tests) and no orphan code (behavior with no requirement). Cross-module chains stay consistent with the Phase 6 Cross-Module Consistency Matrix. After synthesis, the Phase 9 Gap Analysis Matrix confirms the implementation matches the spec, not just that tests pass.
* **Deployment & support scripts:** any script generated to support dev/stage/prod deployment is documented (purpose, invocation, environment, traces-to) in the same commit that adds it — an undocumented script is drift, same as an undocumented requirement.
* **Test & debug tooling:** any test runner, fixture generator, debug helper, or diagnostic script is registered in `test_tooling.md` (name, purpose, invocation, parameters, environment & preconditions, docs link, status) in the same commit that adds, changes, or removes it. Agents read the register before creating a tool — an unregistered tool is drift and gets reinvented.
* **Release / merge to main:** bump the concept document version so each released state of the code has a matching, identifiable concept version.

The litmus test extends to maintenance: hand the *current* concept document to a fresh agent at any time, and it should be able to reproduce the *current* code's behavior. If it cannot, concept and code have drifted and must be reconciled.

---

## Anti-Patterns

| Don't | Do Instead |
| --- | --- |
| Jump to architecture before requirements are stable | Complete Phases 1–4 first |
| Write requirements as prose paragraphs | Use numbered tables (`F-1`, `NF-1`, `U-1`) |
| Skip the artifact interaction analysis | Phase 3 always finds hidden conflicts |
| Leave open questions dangling | Resolve or explicitly defer with clear rationale |
| Write architecture without traceability | Every module must explicitly trace to numbered requirements |
| Write tests without a coverage matrix | Ensure every requirement maps cleanly to a test |
| Let the AI invent solutions during requirements phase | Enforce: "We are discussing requirements here, solutions come later" |
| Ship a prototype without extracting a concept doc | Every production module needs a concept document (Path B, steps 2–5) |
| Reference "the code" for details in concept docs | The document must be self-contained — embed the domain knowledge |
| Let the AI guess domain jargon | Build and maintain a ubiquitous language in a centralized `CONTEXT.md` file |
| Synthesize the entire architecture all at once | Synthesize in small, deliberate vertical slices using continuous TDD cycles |
| Over-engineer with speculative abstractions or flexibility | Simplicity first — minimum code that satisfies the requirement (see RULES.md §10) |
| Make drive-by refactors or edits to unrelated code | Surgical changes — every changed line traces to a requirement |
| Assume a cross-cutting requirement is met because each module looks fine alone | Phase 6 traces every cross-module chain end-to-end (source → transport → destination) |
| Assume a passing test means the real behavior is implemented | Phase 9 traces spec vs. actual implementation — a test can pass against a stub or mock |
| Generate deployment/support scripts and leave them undocumented | Document every script's purpose, invocation, environment, and traceability in the concept or `deployment.md`, same commit |
| Write a new test or debug script without checking what already exists | Read `test_tooling.md` first, reuse; register every new/changed/removed tool in the same commit |
| Ignore software entropy after automated synthesis | Periodically run an architectural review phase to execute deepening opportunities |

---

## Document Structure Template

```markdown
# [Feature] Concept — Consolidated Requirements

## 0. Synthesis Context
    ### Development Path (A: Frontloaded / B: Retrofitted)
    ### Synthesis Dependencies (other concept docs this feature requires)
    ### Synthesis Scope (what can be regenerated from this doc alone)
## 1. Purpose
## 2. Ubiquitous Language & Context (Shared Domain Dictionary / Taxonomy)
## 3. Functional Requirements (F-1 ... F-N)
## 4. User Interaction Requirements (U-1 ... U-N)
## 5. Non-Functional Requirements (NF-1 ... NF-N)
## 6. Integration Points
## 7. Out of Scope
## 8. Resolved Questions & ADRs (Q1 ... QN → Decision / Rationale)
## 9. Artifact Interactions & Interferences
## 10. Schema Extensions
## 11. Architecture & Vertical Slices
    ### Module Overview & Tree
    ### Vertical Slices Breakdown
    ### Module Specifications (class + method signatures in pseudocode)
    ### Pipeline Flow (mermaid flowchart)
    ### Inter-Module Interfaces Table
    ### State Machine Diagram (mermaid)
    ### Design for Testability
    ### Requirement Traceability Matrix
## 12. Architecture Consistency Review
    ### Cross-Module Consistency Matrix (requirement × element → verdict)
    ### Findings & Human-Review Decisions (accepted risks, deferrals, fixes)
## 13. Test Concept & TDD Contract
    ### Unit Tests & Test Case Tables (per module)
    ### Integration Tests (E2E scenarios)
    ### Synthetic Test Data Strategy (templates, variations, golden outputs)
    ### CI Test Constraints (mocking, execution rules)
    ### Test Coverage Matrix & Untested Justifications
    ### Test & Debug Tooling Register (→ companion test_tooling.md)
## 14. Synthesis Gap Analysis
    ### Gap Analysis Matrix (requirement / module / test → verdict)
    ### Findings & Human-Review Decisions (accepted gaps, deferrals, fixes)
## 15. Deployment & Operational Scripts
    ### Script Inventory (name, purpose, target environment(s), invocation, required env vars/secrets, preconditions, idempotency & rollback, traces-to)
    ### (may instead be maintained in a companion `deployment.md`)
## 16. Test & Debug Tooling Register
    ### Tool Inventory (name, purpose, invocation, key parameters, environment & preconditions, docs link, status active/obsolete)
    ### (maintained in companion `test_tooling.md`; referenced from `CLAUDE.md`)

```