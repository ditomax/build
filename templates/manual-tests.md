---
stage: shared
owner: build-synthesis
status: in_progress
revision: 1
created: <YYYY-MM-DDThh:mm>
updated: <YYYY-MM-DDThh:mm>
input: —
---

# Manual Test Manual

_Tests that cannot or should not be automated: user interface on real devices, hardware, look and feel, accessibility with assistive technology, sponsor acceptance, exploratory sessions. A lightweight form of ISO/IEC/IEEE 29119-3 test case specification. Every case traces to requirements; a requirement covered only by a manual case counts as IMPLEMENTED in the gap analysis (MANIFEST Phase 9) only when the case has a passing execution on the current release candidate. Lives with the code (`planning/build/`); an existing manual test document of the project is adopted instead of this file._

## Test cases

### MT-<FEAT>-1 — <short name>

- **Traces to:** <F-<FEAT>-n, U-<FEAT>-n, SC-<FEAT>-n>
- **Why manual:** <device / hardware / human judgement / one-off acceptance>
- **Preconditions:** <build or version, device, data set (from `agent-testing.md` section 3), accounts by role — never real credentials here>
- **Steps:**
  1. <action>
  2. <action>
- **Expected result:** <observable, falsifiable>
- **Duration:** <minutes>

## Execution log

Newest first. One row per execution; a failed case gets a row too, with the finding.

| Date | Case | Build / commit | Environment (device, OS, browser) | Who | Result (pass / fail / blocked) | Finding / note |
| --- | --- | --- | --- | --- | --- | --- |
| | | | | | | |

## Exploratory sessions

Time-boxed, charter-driven sessions. Findings become requirements (through a concept redo), bugs, or new MT cases.

| Date | Charter (what to explore, which risk) | Time box | Who | Findings |
| --- | --- | --- | --- | --- |
| | | | | |

## Notes (human)

<!-- Off limits for all skills. Copied verbatim on every rewrite. -->
