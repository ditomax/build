---
stage: control
owner: build
status: in_progress          # open | in_progress | done | skipped
revision: 1
created: <YYYY-MM-DDThh:mm>
updated: <YYYY-MM-DDThh:mm>
input: —
# --- only in 00-build.md ---
title: <working title of the product>
code_root: <path to the product code, relative to <work> or absolute — never inside <work>>
git: no                      # yes | no — checked by the Director at start
language: en                 # language of the result files' content (requirements and code stay English)
budget_min: {intake: 60, concept: 120, synthesis_per_slice: 45, rescue: 60}
profile: none                # none | profile/
contract_in: none            # none | H2/3 | H2/2 | H2/1 (maquette brief, the version it declares) | register/1 (idea register, Path R) | H1/2 | H1/1 (shortlist entry, Path S)
register: —                  # Path R only: the register version every R-ID is traced against, relative to <work>, e.g. ../idea/10-register.md@3
---

# Build: <title>

<!-- This file belongs to the Director. No other skill writes here. One "Feature" block per feature; the first feature is the scope of the brief. -->

## Starting point

- **Input:** <brief <code> (<path to 60-brief.md>@<rev>) + vcode/ | register <code> (<path>@<rev>) | shortlist entry <ID> (<path>@<rev>) | problem statement in prose>
- **Sponsor / product owner:** <role — the person who signs the PM gate>
- **Decision date:** <YYYY-MM-DD | open — from the register's or the entry's handover, or asked>
- **Purpose of this build:** <one sentence: what ships when this is done?>

## Features

| Code | Title | Path | Field of application | Concept version | Status |
| --- | --- | --- | --- | --- | --- |
| <FEAT> | <title> | <A | B | mixed | R | S | A+R | B+R | mixed+R> | <web app | backend service | embedded | …> | — | <open | in_progress | done | planned (named by a trace, not started)> |

## Feature <FEAT> — <title>

| Stage | Skill | File | Status | Current revision | Started | Finished | Minutes |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | intake | <FEAT>/10-intake.md | open | — | | | |
| 2 | concept | <FEAT>/20-concept.md | open | — | | | |
| 3 | synthesis | <FEAT>/30-synthesis.md | open | — | | | |
| 4 | rescue | <FEAT>/40-rescue.md | open | — | | | on demand |

<!-- Status values: open | in_progress | done | skipped | stale (input changed, see write rule 6). Rescue is never "stale"; each round is a new .vN file. -->

### Gates <FEAT>

| Gate | MANIFEST | When | Decision | By | Date | Rationale / accepted items |
| --- | --- | --- | --- | --- | --- | --- |
| PM review | Phase 4 | after concept §3–§10, before architecture | <pending | approved | changes requested> | | | <Path R: trace count — MUST → requirement · other feature · deferred; R-M priorities confirmed> |
| Consistency review | Phase 6 | after architecture, before synthesis | <pending | clean | accepted with findings | fix requested> | | | |
| Gap analysis | Phase 9 | after the last slice | <pending | clean | accepted with gaps | back to synthesis> | | | |

## Cross-feature interactions

<!-- Maintained by the Director from every feature's 20-concept.md §0 (dependencies) and §9 (interactions & interferences). One row per pair. Empty with one feature. -->

| Feature | Depends on | Touches | Interference risk | Where documented |
| --- | --- | --- | --- | --- |
| — | — | — | — | — |

## Decision log

| Time | Feature | Stage | Decision | Note |
| --- | --- | --- | --- | --- |
| <hh:mm> | <FEAT> | — | start | path <B>, git <no>, code root <…> |

<!-- Allowed decisions: start · next · redo · stop · skip · gate:<name>:<result> · budget_exceeded (with the user's answer) · new_feature -->

## Open items for the Director

<!-- Exceeded budgets, conflict files, stale downstream files, pending gate decisions, drift reports. Empty = nothing open. -->

- —

## Notes (human)

<!-- Off limits for all skills. The human corrects, adds and comments here. Copied verbatim on every rewrite. -->
