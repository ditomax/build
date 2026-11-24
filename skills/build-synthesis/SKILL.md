---
name: build-synthesis
version: "0.5"
description: >
  Stage 3 of the build skillset. Writes <FEAT>/30-synthesis.md (MANIFEST Sections
  13–16) and the product code under code_root — test concept first, then a strict
  red-green-refactor loop one vertical slice at a time, after a slice 0 that lays
  the foundation (layout, test documentation, dev CLI, repository hygiene),
  registering every script and tool in deployment.md / agent-testing.md, and
  closing with the synthesis gap
  analysis for the human gate. Called by the build Director; the only stage besides
  rescue that writes code.
---

# synthesis — Stage 3

You are the **build team**: you write tests, then the minimum code that passes them, slice by slice, and you never let the concept and the code drift apart. Read `RULES.md` (suite root, RULES §2) first — §10 (working principles) binds every line you write; the process is `MANIFEST.md` Phases 7–9.

**Input:** `<FEAT>/20-concept.md` (current version), `CONTEXT.md`, `decisions/` (the layout ADR first), `layouts/<profile>.md`, `agent-testing.md` (or a legacy `test_tooling.md`), `manual-tests.md`, `deployment.md`, `code_root`, profile (stack and IT constraints). **Output:** `<FEAT>/30-synthesis.md` from `templates/30-synthesis.md`; code and tests under `code_root`; register updates. **Budget:** `budget_min.synthesis_per_slice` per slice.

Lineage: MANIFEST Phases 7–9; the Definition of Done (RULES §11); maquette-build's slice discipline and maquette-harden's one-commit-per-fix.

## Opening

Two sentences: the test plan, then the code slice by slice with tests green after each; how many slices the concept lists and the budget per slice.

## Sequence

### 1. Test concept (Phase 7, §13) — before any product code

Unit test tables per module (input → expected → requirement), integration scenarios along the slices, synthetic data strategy (templates + variations, golden outputs, versioned in the repo), CI constraints (no LLM, no network, mocks named), coverage matrix — every requirement → at least one test; justify every exception. *Constraint and UCA tests*: one test per UCA and per SC of concept §5 / §9 — negative (the unsafe action is refused), fault injection (the dependency fails, the feedback is missing) or adversarial (the injection set: manipulated documents, tool results, prompts — `checklists/ai-threats.md`); an SC enforced by *prompt + test* gets its test here. *Error matrix tests*: one test per E row of concept §11 *Error handling* — inject the error at its detector, assert the row's action and its log entry (and where it is kept), then pass the bound and assert the escalation (a row with sub-cases or several reactions gets one test per branch); a safe state and an 'n in a row' counter are also checked across a restart. Time bounds use the clock seam and `run_once(now)` named in concept §11 *Design for testability* — never real waiting. **From the supported entry:** besides unit tests of the handler, each distinct outcome of a row gets at least one test that starts at the product's supported entry point (the CLI command, the scheduled job, the HTTP route, the inbound webhook) — an external trigger is exercised through the entry that consumes it — advances past each deadline and asserts the **actual recipient**, the **persistent effect** and the **recovery state**, not only that a callback ran. **Counter-probe:** for each row, disable one required destination or fail one recovery write and show that a test fails; a probe that leaves the suite green is a missing test. A trigger with no consuming entry in the product is recorded as PARTIAL. **Outside-action tests** (concept §11 standing rows, §9 *Outside actions*): for each outside action, raise an unexpected exception **before, during and after the commit boundary** — assert the behaviour the concept declared for each of the three (whatever it is: hold, complete, compensate, protective step, reconcile, hand to a person — never repeated blindly where the outcome is unknown), context stored, alarm over the independent channel; kill the process in the middle of the action — the named external remedy acts (watchdog, redundant component, receiver default); kill and restart, and let each repeater of the action's row fire — the environment's (intake I13), the product's own re-poll of unacknowledged input, a restore of its own state, a person redoing it — assert the action's **declared guarantee** and its reconciliation path: exactly once, compensated, or *at most once, then a person* (never twice, an unknown outcome reaches the person), and a safe state and its counters survive the restart. These tests are the minimum, not proof of the SC: where a gate loss depends on timing, misleading feedback, partial effects, common mode or recovery interactions, test those too. A calculation on a gate loss is checked against its named independent oracle (historic figures, a hand calculation, a parallel run), not only against expected values the same author derived. For each entry point, an error outside any outside action takes the entry point's declared handling, checked against what the run had already started. For an output someone acts on: one test each for wrong · missing · late and for its absence detector. **Remedies** are tested by what the product can show: the stored information is enough to choose the remedy, and the immediate action holds until it; the person's part is a manual case or a drill in `manual-tests.md`. A UCA in an E row's cell is covered by that row's test **only if the test asserts the UCA's context**; a UCA about the row's own detector, channel or a person's inaction gets its own test outside the product (monitoring check, an `MT` case) or is marked "not testable in-product" in the coverage matrix. A *prompt + test* SC: CI tests the code envelope around the model with a stub (no LLM in CI); the model test itself runs as a named evaluation outside CI before every release and is listed in `agent-testing.md`. Fill the "Test" column of the concept's traceability matrix by reference (you do not edit `20-concept.md`; list requirement → test here, the Director checks both agree). *Manual tests*: what cannot or should not be automated (UI on a real device, hardware, look and feel, sponsor acceptance) becomes an `MT-<FEAT>-n` case in `manual-tests.md` with its traces, and appears in the coverage matrix as `manual`. Read `agent-testing.md` first — tools, fixtures and the fix journal — and reuse what exists; the test runner comes from the layout profile. Write `30-synthesis.md` revision 1 with §13 filled and hand back briefly — "test plan ready, starting S1?" — then continue on the user's word.

### 1a. Slice 0 — foundation (RULES §15, §13), before S1

**Layout and tools** (RULES §15). First feature: read the layout ADR and `layouts/<profile>.md`; write the profile's skeleton for this feature (manifest, core unit, the feature's unit, entry point, test folders), the shared tools from `layouts/_tools.md` verbatim (`tools/dev.py` with the profile's PROFILE block, the layout check, the settings source), the profile's ignore lines, and — if absent — `agent-testing.md`, `manual-tests.md`, `cli.md`, `configuration.md` in `<work>` from the templates (an existing manual test document is adopted; a legacy `test_tooling.md` has its rows moved into `agent-testing.md` section 1 and becomes a one-line pointer). Run `python tools/dev.py docs`, then `check`. Check that `planning/build/` is not git-ignored (`git check-ignore -v planning/build/00-build.md` must print nothing); an ignore rule that catches it is fixed by anchoring (`/build/`). Existing files are merged, never overwritten; existing code keeps its layout (L7). Later features: add only the feature's unit, its test folder and its rows; re-run `check`.

**Repository hygiene** (RULES §13). Read `templates/repo-hygiene.md` and copy its blocks verbatim — never retype them from memory. First feature: all of H1–H7; later features: re-check H1 and H5 only, unless a row of `00-build.md` *Repository hygiene* is open. Say in one block which files you add or merge in `code_root` (layout, tools, docs, hygiene) and which installs you need, then ask **Y0** once: "go — and may I install <the profile's environment, pre-commit, gitleaks>?" Write the files either way (merge, never overwrite; skip H3 on a host other than Claude Code and say so); install only on a yes. Run the H1 checks (`git check-ignore -v`, `git ls-files` — read-only) and the H5 test; a tracked host-local file is reported to the user with the `git rm --cached` command, never removed by you. If `git remote -v` names GitHub (or another host with push protection), ask **Y0G**. Write the S0 block of the slice log, commit per RULES §6 (`build(<FEAT>): S0 foundation — layout <profile>, test docs, dev CLI, repository hygiene`), and hand back to the Director with the layout check result and the state per control (H1–H7) before S1.

### 2. Slices (Phase 8) — repeat per slice, in the concept's order

For each slice: say which slice and which requirements; **red** — write the failing tests from §13; **green** — the minimum code that passes, in the modules the concept names, using `CONTEXT.md` terms for identifiers; **refactor** — simplify without changing behaviour, tests stay green; run the whole suite and `python tools/dev.py check`, report `n passed / m total` with the command. Where the profile's toolchain is missing here (its *Agent limits*), say so before the slice, write the code and tests anyway, and record "not verified here — run `<command>` on <platform>": the slice is green only after the user reports the result (RULES §15). Then:

- Any script created for setup, seed, deploy, rollback or smoke check → `deployment.md` in the same step; any test, fixture, debug or diagnostic tool → `agent-testing.md` section 1 (fixtures: section 3) in the same step; a new dev CLI command or setting → code, then `python tools/dev.py docs`, same commit. Unregistered = not done.
- A test failure that needed more than one attempt or a non-obvious fix → a **fix journal** entry in `agent-testing.md` section 4 (symptom → cause → fix → test / commit) in the same commit; a standing trap → section 5.
- New code goes where the layout ADR says; a new top-level folder or a dependency the layout check refuses is a concept question (L5), never a workaround.
- A requirement discovered while coding: do not implement it silently. Record it under "Requirements discovered" in the slice log and tell the Director a concept redo is needed; continue only with what the current concept covers.
- Drift you notice (code needs something the concept does not say) is handled the same way — document first, code follows.
- Every slice adds, for each side-effecting call it introduces, the invariant checks right before it and the action-boundary helper carrying its standing row (concept §11, profile §8); besides that helper, a catch-all exists only at entry points, as backstop.
- Errors are handled only as the concept says — their E row, the requirement of a scenario answer, an operational requirement or the outside action's standing row — following the profile's error convention (`layouts/<profile>.md` §8): caught where the row's action can be taken, otherwise propagated with their context; each catch site names its E-ID or requirement ID in a comment. An error the code must handle that maps to none of these — a library exception, a new failure of a dependency — takes the outside action's standing row (outside any: the entry-point backstop) and is logged under "Requirements discovered" for a concept redo with its result from the ignore test (RULES §16 *Living*); never a new ad-hoc handler. An error nobody can classify blocks the slice from going green until the concept redo (Y4).
- With `git: yes (agent commits)`: commit the slice (RULES §6) with `Traces to:` in the body and say so in half a sentence; with `git: yes (user commits)`: write the exact commands into the slice log instead.
- Append the slice block to the slice log (revision + 1), then ask: "next slice, or stop here?"

Stack and dependency choices follow the concept and the profile; a dependency the concept does not name is a question for the user, not a decision. No network, no installs, without saying so first.

### 3. Gap analysis (Phase 9, §14) — once, after the last slice

Walk the traceability matrix requirement by requirement and **open the real implementation**: not a stub, `pass`, `NotImplementedError`, TODO, hard-coded return or mock so heavy the test cannot fail. Walk every module in §11 and every test in §13 the same way. Walk every SC of concept §5 as kind *constraint*: IMPLEMENTED only when the SC — or the E rows and requirements that carry it out — is in the code and its test passes. Walk every E row of concept §11 as kind *error*: IMPLEMENTED only when detector, action, bound with escalation and log are in the code, its test passes — the escalation included —, each outcome has a test from the supported entry and the counter-probe fails a test; otherwise PARTIAL. Every outside action's standing row of concept §11 is walked the same way — its tests before, during and after the commit boundary, kill, restart and repeaters; an error still unclassified is MISSING. Walk `agent-testing.md` section 1 in both directions (orphans, ghosts), compare its CLI quick reference with `python tools/dev.py --help`, and prune obsolete rows. Walk `manual-tests.md`: a requirement covered only by an `MT` case is IMPLEMENTED only with a passing execution on the current release candidate — otherwise PARTIAL, and the case is handed to the user to run. Run `python tools/dev.py check` (layout, docs in sync) and record its result as kind *layout*. Produce the gap matrix — always, even when clean. Hand back with the count of PARTIAL / MOCKED / MISSING rows. The Director asks the gate question; "back to synthesis" means more slices in this file; "accepted" means the human's rationale goes into §14.

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
| Put a secret into code, a fixture, a command or a commit message | Environment variable or `.env`, referred to by name; fake secrets in tests are assembled at runtime (RULES §10.6) |
| Commit with `--no-verify` when the guard blocks | Fix the finding; a blocked commit is the guard working |
| Skip an adversarial test because "the model won't do that" | For a prompt + test constraint, the test is the enforcement |
| Add "flexibility", abstractions, error handling for impossible cases | Minimum code; RULES §10 |
| Catch an error and carry on with an earlier value, or retry without a bound | Only the action of its E row; no row → concept redo |
| Signal a failure by a value the caller can mistake for a result (`False`, `None`, `-1`) | The profile's error convention (`layouts/<profile>.md` §8) |
| Touch `vcode/` or the brief | Read for inspiration, never write |
| Invent folders, or put code where the layout check refuses it | The layout ADR decides; a new top-level folder is a concept question (RULES §15 L5) |
| Report a slice green that you could not build or run here | "Not verified here — run `<command>` on <platform>"; green after the user's result |
| Fix a stubborn test and move on silently | Fix journal entry in `agent-testing.md`, same commit |
| Call a requirement done because its manual case exists | Only a logged passing execution on the release candidate counts |
