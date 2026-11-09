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
kind: maintenance             # maintenance | incident (CAST-light) | audit (secret audit, read-only)
budget_refactors: <n>
---

# Rescue <FEAT> — round <n>

_MANIFEST Phase 10. A periodic maintenance round against software entropy: find deepening opportunities, apply them one at a time within budget, revert on regression, and reconcile concept and code. Each round is its own file._

## 0. Review triggers (R0)

<!-- Asked by the Director at the start of the round. Any yes → concept redo of §5 / §9 for this feature, or this round becomes an incident round. -->

| Since the last concept version | Changed? | What |
| --- | --- | --- |
| AI model or model version | <yes | no> | <…> |
| Data source | <yes | no> | <…> |
| Tool or permission of an AI component | <yes | no> | <…> |
| Exposure (internal → partner → public) | <yes | no> | <…> |
| Learning loop | <yes | no> | <…> |
| Incident | <yes | no> | <…> |

## 1. Scan

- **Scope scanned:** <code_root paths, concept sections, ADRs>
- **Drift found (concept ≠ code):** <none | list with requirement IDs>
- **Complexity hotspots:** <modules, why>

## 1a. Incident analysis (CAST-light)

<!-- Incident rounds only; otherwise "not relevant — maintenance round". No blame on persons or the model: the question is why the control structure allowed it. -->

| What happened | Loss | Failed control loop | Why the structure allowed it | Fix | Enforced by | Remaining gap |
| --- | --- | --- | --- | --- | --- | --- |
| <…> | <L…> | <missing SC | SC not enforced | feedback missing | process model wrong | conflicting controllers | authority too wide> | <…> | <new or changed SC-<FEAT>-n → concept redo, regression test> | <now → target> | <…> |

## 1b. Secret audit

<!-- RULES §13, templates/repo-hygiene.md A1–A4. Every round: A3 (controls in place). Audit rounds: A1–A4 in full, and sections 2–4 read "not relevant — audit round". Never a secret value, not even partly. -->

- **Scope:** <A3 only (maintenance / incident round) | A1–A4 (audit round): branches <n>, commits <n>, tools gitleaks <version> | not run — declined>

| # | Where (file · line or commit) | Pattern type | Source (A1 history · A2 allow list · A3 control) | Status |
| --- | --- | --- | --- | --- |
| <1> | <…> | <…> | <…> | <open | rotated | removed from tracking | accepted (by, date)> |

- **Order of action** (with any secret finding): 1. rotate every secret listed — it is compromised; 2. then clean history (`git filter-repo` or BFG, force-push, every clone re-cloned) — the user's job; 3. re-run the audit.
- **Allow-list hygiene (R8):** <overly literal entries and the generalized rule proposed for each | none>

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
