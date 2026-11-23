---
stage: synthesis
owner: build-synthesis
status: in_progress          # open | in_progress | done | skipped
revision: 1
created: <YYYY-MM-DDThh:mm>
updated: <YYYY-MM-DDThh:mm>
input: <FEAT>/20-concept.md@<rev> (version <x.y>)
feature: <FEAT>
git: no                      # copied from 00-build.md
---

# <FEAT> Synthesis — Test Concept, Slice Log, Gap Analysis

_Sections 13–16 of the MANIFEST document structure plus the slice log. Written before the first line of product code (Section 13), updated after every slice (slice log), closed by the gap analysis (Section 14)._

## 13. Test Concept & TDD Contract

### Unit tests per module

| Module | Test | Input → expected | Requirement |
| --- | --- | --- | --- |
| <…> | <test name> | <…> | F-<FEAT>-x |

### Integration tests (end to end)

| Scenario | Path through the system | Synthetic input | Expected | Requirements |
| --- | --- | --- | --- | --- |
| <…> | <…> | <…> | <…> | <…> |

### Constraint and UCA tests

<!-- One row per UCA and per SC of 20-concept.md §5 / §9. Kind: negative (the unsafe action is refused) · fault injection (the dependency fails, the feedback is missing) · adversarial (manipulated documents, tool results, prompts — CS3, checklists/ai-threats.md). An SC enforced by "prompt + test" needs its test here. -->

| UCA / SC | Test | Kind | Requirement | Status |
| --- | --- | --- | --- | --- |
| <UCA-<FEAT>-n / SC-<FEAT>-n> | <test name> | <negative | fault injection | adversarial> | <NF-<FEAT>-n> | <planned | green | red> |

### Error matrix tests

<!-- One row per E row of 20-concept.md §11 Error handling (RULES §16), the harmless row and outcome-rate bands included, and per outside action: unexpected exception before · during · after the commit boundary → its standing row; killed process → external remedy acts; kill + restart and each repeater of the action's row (environment, own re-poll, restore, person) → the declared guarantee (exactly once · compensated · at most once with the unknown outcome at the person) and its reconciliation path (E-ID column: the action name). A minimum, not a proof. The test injects the error at its detector, asserts the row's action and log entry, then passes the bound (clock seam, run_once(now) — no real waiting) and asserts the escalation — one test per branch where a row has sub-cases or several reactions; safe states and 'n in a row' counters are also checked across a restart. A UCA from the row's cell goes into the second column only if this test asserts its context; otherwise it gets its own test outside the product or "not testable in-product" in the coverage matrix. -->

| E-ID | Branch | Also covers | Test | Entry point | Injects | Asserts (recipient · persistent effect · recovery state · escalation after the bound) | Counter-probe (fails a test?) | Status |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| <E-<FEAT>-n> | <branch> | <UCA-<FEAT>-k | —> | <test name> | <CLI command | job | route | webhook | unit only> | <…> | <…> | <destination disabled / recovery write failed → <test> fails | no test fails → missing test> | <planned | green | red> |

### Synthetic test data strategy

<templates + variations, golden outputs, the injection set for adversarial tests (manipulated documents, tool results, prompts), where they live in the repo, how they are versioned>

### CI constraints

<no LLM calls, no network, all external dependencies mocked; how the suite is run; time limit>

### Manual tests

<!-- What cannot or should not be automated: UI on real devices, hardware, look and feel, sponsor acceptance. The cases themselves live in ../manual-tests.md (MT-<FEAT>-n with steps and execution log); list them here with their requirements. -->

| Case | Requirement(s) | Why manual |
| --- | --- | --- |
| <MT-<FEAT>-n | none> | <…> | <…> |

### Test coverage matrix

| Requirement | Test(s) — automated or `MT-<FEAT>-n` | Untested — justification |
| --- | --- | --- |
| F-<FEAT>-1 | <…> | — |

### Test & debug tooling

→ project-wide agent test manual `../agent-testing.md` (read before writing any tool; register tools, fixtures and fixes in the same commit)

## Slice log

<!-- One block per slice, in order. Written after the slice, never before. -->

### S0 — foundation

- **Layout (RULES §15):** <profile> per `decisions/ADR-<FEAT>-<nnn>-layout.md` · skeleton written: <files and folders> · tools: `tools/dev.py`, <layout check> · docs: agent-testing.md · manual-tests.md · cli.md · configuration.md <created | adopted | already present>
- **Layout checks:** <`python tools/dev.py check`: clean | findings> · <planning/build/ git-ignored: no | fixed (rule …)> · <not verified here — run … on … | n.a.>
- **Repository hygiene — traces to:** SC-HYG (RULES §13), L9
- **Files written or merged:** <.gitignore (H1) · .dockerignore (H2 | n.a.) · .claude/settings.json (H3 | n.a. — host) · AGENTS.md / CLAUDE.md (H4) · tools/secret_check.py + tests/test_secret_check.py (H5) · .pre-commit-config.yaml (H6)>
- **Checks:** <check-ignore -v → this repo's .gitignore: yes | no> · <tracked host-local files: none | list> · <H5 test: n passed>
- **Installs (Y0):** <pre-commit + gitleaks installed, `pre-commit install` run | declined>
- **Push protection (Y0G):** <on | off — finding | no remote yet | not GitHub: …>
- **Commit:** <hash and message — or commands for the user — or "— (no git)">

### S1 — <name>

- **Traces to:** <…>
- **Red:** <failing tests written>
- **Green:** <files created or changed under code_root>
- **Refactor:** <what was simplified, or "none">
- **Tests:** <n passed / m total, command>
- **Requirements discovered:** <none | list → concept redo requested>
- **Scripts / tools added:** <registered in deployment.md / agent-testing.md, or "none">
- **Fix journal:** <entries added to agent-testing.md §4, or "none">
- **Verified here:** <yes | not verified here — run `<command>` on <platform>; green after the user's result>
- **Commit:** <hash and message — or "— (no git)">
- **Minutes:** <…>

## 14. Synthesis Gap Analysis

<!-- MANIFEST Phase 9. Runs once after the last slice. Open the real implementation for every row — never infer from names, docstrings or a passing test. -->

### Gap analysis matrix

| Item | Kind | Traces to | Verdict | What is missing |
| --- | --- | --- | --- | --- |
| <requirement / module / test / constraint / error row / manual case / layout> | requirement / module / test / tool / constraint / error / manual / layout | <…> | IMPLEMENTED / PARTIAL / MOCKED / MISSING | <…> |

<!-- Kind "manual": IMPLEMENTED only with a passing execution of the MT case on the current release candidate (../manual-tests.md). Kind "layout": the result of `python tools/dev.py check` — layout rules and generated docs in sync. -->

<!-- Kind "error": one row per E row of concept §11 — IMPLEMENTED only when detector, action, bound with escalation and log are in the code and the row's test passes, the escalation included. -->

<!-- Kind "constraint": one row per SC — IMPLEMENTED only when the SC (or what carries it out: E rows, requirements) is in the code and its test passes. An SC left PARTIAL or MISSING is accepted only as a residual risk with name and date below. -->

### Tooling register check

- **Orphans** (scripts in the repo, not registered): <none | list>
- **Ghosts** (registered, script missing): <none | list>

### Findings & human-review decisions

| Finding | Decision | By | Date | Rationale / follow-up |
| --- | --- | --- | --- | --- |
| <…> | accepted / back to synthesis / deferred | <…> | <…> | <…> |

## 15. Deployment & Operational Scripts

→ project-wide register `../deployment.md`

## 16. Test & Debug Tooling Register

→ project-wide agent test manual `../agent-testing.md` (tool register, CLI quick reference, fixtures, fix journal, pitfalls) · manual cases `../manual-tests.md` · dev CLI `../cli.md`

> Produced with the skill suite (guidelines only) — review and responsibility lie with the operator; see `DISCLAIMER.md`.

## Notes (human)

<!-- Off limits for all skills. Copied verbatim on every rewrite. -->
