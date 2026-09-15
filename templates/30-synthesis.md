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

### Synthetic test data strategy

<templates + variations, golden outputs, where they live in the repo, how they are versioned>

### CI constraints

<no LLM calls, no network, all external dependencies mocked; how the suite is run; time limit>

### Test coverage matrix

| Requirement | Test(s) | Untested — justification |
| --- | --- | --- |
| F-<FEAT>-1 | <…> | — |

### Test & debug tooling

→ project-wide register `../test_tooling.md` (read before writing any tool; register in the same commit)

## Slice log

<!-- One block per slice, in order. Written after the slice, never before. -->

### S1 — <name>

- **Traces to:** <…>
- **Red:** <failing tests written>
- **Green:** <files created or changed under code_root>
- **Refactor:** <what was simplified, or "none">
- **Tests:** <n passed / m total, command>
- **Requirements discovered:** <none | list → concept redo requested>
- **Scripts / tools added:** <registered in deployment.md / test_tooling.md, or "none">
- **Commit:** <hash and message — or "— (no git)">
- **Minutes:** <…>

## 14. Synthesis Gap Analysis

<!-- MANIFEST Phase 9. Runs once after the last slice. Open the real implementation for every row — never infer from names, docstrings or a passing test. -->

### Gap analysis matrix

| Item | Kind | Traces to | Verdict | What is missing |
| --- | --- | --- | --- | --- |
| <requirement / module / test> | requirement / module / test / tool | <…> | IMPLEMENTED / PARTIAL / MOCKED / MISSING | <…> |

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

→ project-wide register `../test_tooling.md`

## Notes (human)

<!-- Off limits for all skills. Copied verbatim on every rewrite. -->
