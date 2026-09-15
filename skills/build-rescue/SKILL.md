---
name: build-rescue
version: "0.1"
description: >
  Stage 4 of the build skillset, on demand and repeatable. Writes
  <FEAT>/40-rescue.md — scans code, CONTEXT.md and decisions/ for entropy and
  concept/code drift, applies deepening refactors one at a time within budget with
  revert on regression, and reports the concept changes needed. Called by the
  build Director after synthesis has produced at least one slice.
---

# rescue — Stage 4

You are the **maintenance crew**: you make the codebase simpler without changing what it does, and you report where concept and code have drifted. Read `../../RULES.md` first (§10 binds you); the process is `../../MANIFEST.md` Phase 10 and "Concept ↔ Code Synchronization".

**Input:** `<FEAT>/20-concept.md`, `<FEAT>/30-synthesis.md`, `CONTEXT.md`, `decisions/`, `test_tooling.md`, `code_root`. **Output:** `<FEAT>/40-rescue.md` (a new `.vN` per round) from `templates/40-rescue.md`; refactored code under `code_root`. **Budget:** `budget_min.rescue`; `budget_refactors` = 5 with git, 2 without.

Lineage: MANIFEST Phase 10; maquette-harden (one commit per fix, revert on regression).

## Opening

Two sentences: a maintenance round — find what got tangled, untangle the most valuable pieces one at a time, and say what the concept must learn from it.

## Sequence

1. **Scan.** Read the concept's traceability matrix against the code: modules that exist but are not in §11, behaviour with no requirement, requirements whose module changed shape. List drift with IDs. Then look for deepening opportunities: interfaces that could be simpler, duplicated logic, names that left `CONTEXT.md`, dead code, modules doing two things. Rank by expected gain × low risk.
2. **Propose.** Show the ranked table and ask: "which ones, in this order?" — one question.
3. **Apply, one at a time.** Run the full test suite before; apply the refactor; run it after. Green → keep (with git: commit, RULES §6). Any regression → revert immediately, record the reason, move on. Stop at the refactor budget or the time budget and say which.
4. **Reconcile.** Every kept refactor that changes a signature, module boundary or term is a concept change: list the exact sections; the Director schedules a concept redo (version bump). Add ADRs for decisions taken here. Update `test_tooling.md` if a tool changed.
5. **Write** `40-rescue.md` and hand back per RULES §7 with: kept / reverted counts, drift items, concept sections to update.

## Anti-patterns

| Don't | Do instead |
| --- | --- |
| Refactor and add features in one go | Behaviour-preserving only; features are slices |
| Batch several refactors into one commit | One each; revert is cheap only when isolated |
| Fix drift by editing the concept yourself | Report the sections; concept redo is the concept stage's job |
| Keep a refactor with a red test "to fix later" | Revert; note it as an opportunity for the next round |
| Rename without updating `CONTEXT.md` | The dictionary moves with the code |
