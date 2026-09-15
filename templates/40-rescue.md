---
stage: rescue
owner: build-rescue
status: in_progress          # open | in_progress | done | skipped
revision: 1
created: <YYYY-MM-DDThh:mm>
updated: <YYYY-MM-DDThh:mm>
input: <FEAT>/20-concept.md@<rev>, <FEAT>/30-synthesis.md@<rev>, ../CONTEXT.md@<rev>, ../decisions/
feature: <FEAT>
git: no
budget_refactors: <n>
---

# Rescue <FEAT> — round <n>

_MANIFEST Phase 10. A periodic maintenance round against software entropy: find deepening opportunities, apply them one at a time within budget, revert on regression, and reconcile concept and code. Each round is its own file._

## 1. Scan

- **Scope scanned:** <code_root paths, concept sections, ADRs>
- **Drift found (concept ≠ code):** <none | list with requirement IDs>
- **Complexity hotspots:** <modules, why>

## 2. Opportunities

| # | Opportunity | Kind | Expected gain | Risk | Traces to |
| --- | --- | --- | --- | --- | --- |
| 1 | <…> | simplify interface / merge / split / remove dead code / align naming with CONTEXT.md | <…> | low / medium / high | <…> |

## 3. Applied

| # | Change | Tests before → after | Commit | Verdict |
| --- | --- | --- | --- | --- |
| 1 | <…> | <n/m → n/m> | <hash> | kept / reverted (<reason>) |

## 4. Concept reconciliation

- **Concept changes needed:** <none | list → concept redo requested with the exact sections>
- **ADRs added:** <…>

## 5. Recommendation for the next round

<one paragraph>

## Notes (human)

<!-- Off limits for all skills. Copied verbatim on every rewrite. -->
