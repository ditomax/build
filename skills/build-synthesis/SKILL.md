---
name: build-synthesis
version: "0.1"
description: >
  Stage 3 of the build skillset. Writes <FEAT>/30-synthesis.md (MANIFEST Sections
  13–16) and the product code under code_root — test concept first, then a strict
  red-green-refactor loop one vertical slice at a time, registering every script
  and tool in deployment.md / test_tooling.md, and closing with the synthesis gap
  analysis for the human gate. Called by the build Director; the only stage besides
  rescue that writes code.
---

# synthesis — Stage 3

You are the **build team**: you write tests, then the minimum code that passes them, slice by slice, and you never let the concept and the code drift apart. Read `../../RULES.md` first — §10 (working principles) binds every line you write; the process is `../../MANIFEST.md` Phases 7–9.

**Input:** `<FEAT>/20-concept.md` (current version), `CONTEXT.md`, `decisions/`, `test_tooling.md`, `deployment.md`, `code_root`, profile (stack and IT constraints). **Output:** `<FEAT>/30-synthesis.md` from `templates/30-synthesis.md`; code and tests under `code_root`; register updates. **Budget:** `budget_min.synthesis_per_slice` per slice.

Lineage: MANIFEST Phases 7–9; the Definition of Done (RULES §11); maquette-build's slice discipline and maquette-harden's one-commit-per-fix.

## Opening

Two sentences: the test plan, then the code slice by slice with tests green after each; how many slices the concept lists and the budget per slice.

## Sequence

### 1. Test concept (Phase 7, §13) — before any product code

Unit test tables per module (input → expected → requirement), integration scenarios along the slices, synthetic data strategy (templates + variations, golden outputs, versioned in the repo), CI constraints (no LLM, no network, mocks named), coverage matrix — every requirement → at least one test; justify every exception. Fill the "Test" column of the concept's traceability matrix by reference (you do not edit `20-concept.md`; list requirement → test here, the Director checks both agree). Read `test_tooling.md`; if the project has no test runner yet, plan it as the first tool and register it. Write `30-synthesis.md` revision 1 with §13 filled and hand back briefly — "test plan ready, starting S1?" — then continue on the user's word.

### 2. Slices (Phase 8) — repeat per slice, in the concept's order

For each slice: say which slice and which requirements; **red** — write the failing tests from §13; **green** — the minimum code that passes, in the modules the concept names, using `CONTEXT.md` terms for identifiers; **refactor** — simplify without changing behaviour, tests stay green; run the whole suite, report `n passed / m total` with the command. Then:

- Any script created for setup, seed, deploy, rollback or smoke check → `deployment.md` in the same step; any test, fixture, debug or diagnostic tool → `test_tooling.md` in the same step. Unregistered = not done.
- A requirement discovered while coding: do not implement it silently. Record it under "Requirements discovered" in the slice log and tell the Director a concept redo is needed; continue only with what the current concept covers.
- Drift you notice (code needs something the concept does not say) is handled the same way — document first, code follows.
- With `git: yes`: commit the slice (RULES §6) with `Traces to:` in the body. Say so in half a sentence.
- Append the slice block to the slice log (revision + 1), then ask: "next slice, or stop here?"

Stack and dependency choices follow the concept and the profile; a dependency the concept does not name is a question for the user, not a decision. No network, no installs, without saying so first.

### 3. Gap analysis (Phase 9, §14) — once, after the last slice

Walk the traceability matrix requirement by requirement and **open the real implementation**: not a stub, `pass`, `NotImplementedError`, TODO, hard-coded return or mock so heavy the test cannot fail. Walk every module in §11 and every test in §13 the same way. Walk `test_tooling.md` in both directions (orphans, ghosts) and prune obsolete rows. Produce the gap matrix — always, even when clean. Hand back with the count of PARTIAL / MOCKED / MISSING rows. The Director asks the gate question; "back to synthesis" means more slices in this file; "accepted" means the human's rationale goes into §14.

### 4. Release point

When the gate is clean or every item is decided, propose the release: the Director bumps the concept `version` (through a concept redo if content changed, else in `00-build.md`'s feature table) and commits `release <version>`.

## Anti-patterns

| Don't | Do instead |
| --- | --- |
| Write code before the test concept exists | §13 first, then S1 |
| Build several slices at once | One slice, tests green, then the next |
| Implement a discovered requirement on the fly | Log it, request a concept redo, keep the slice within the concept |
| Leave a script or tool unregistered | Same step, same commit |
| Infer a requirement is met from a passing test | Phase 9 opens the implementation |
| Mock so much the test cannot fail | A test that cannot fail is worse than none |
| Add "flexibility", abstractions, error handling for impossible cases | Minimum code; RULES §10 |
| Touch `vcode/` or the brief | Read for inspiration, never write |
