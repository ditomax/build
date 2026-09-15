# build — Shared Rules for All Stage Skills

**Version 1.** Every stage skill reads this file before doing anything. The Director (`build`) enforces it. If a stage skill and this file disagree, this file wins. Where this file is silent on document structure or process, `MANIFEST.md` wins.

## 1. Vocabulary

- **Build** — the work of turning a brief (or a problem statement) into a shipped product through concept documents that code is synthesized from. Named for what it is: the building after the maquette.
- **Feature** — one coherent group of functions with its own concept document. The first feature is the scope of the brief; later features are added one at a time. Feature code: 2–4 letters (`PRC`, `AUTH`).
- **Stage** — one of: intake → concept → synthesis → rescue. Each stage has one owner skill, defined inputs, one result file per feature. Rescue is on demand and repeatable; the other three run once per feature (reruns via `redo`).
- **Concept document** — `20-concept.md` (Sections 0–12 of the MANIFEST template) plus `30-synthesis.md` (Sections 13–16). Together they are the source of truth for the feature's code.
- **Result file** — `NN-<stage>.md` in the feature folder, filled from the matching template in `templates/`.
- **Director** — the `build` skill. Reads the work root, tells the user where they are, calls the stage skill, updates `00-build.md`.
- **Gate** — a point where the human decides before the next stage starts: PM review (MANIFEST Phase 4), consistency review (Phase 6), gap analysis (Phase 9). Gates are Director questions with a logged answer, not stages.
- **Human notes** — the section `## Notes (human)` at the end of every result file.
- **Profile** — an optional `profile/` folder with customer-specific constraints (§8).

## 2. Folder is the state

There is no state outside the work root — no `~/.something`, no environment variables, no memory across projects. A skill learns everything it needs by reading `00-build.md`, its input files and (if present) the profile. A skill that needs something else asks the user.

Two layouts; the Director resolves which applies and calls the work root `<work>`:

| Layout | `<suite>` (read-only) | `<work>` | profile | brief (H2) | product code |
| --- | --- | --- | --- | --- | --- |
| standalone workspace | the folder holding `AGENTS.md`, `VERSION` | `<suite>/builds/<project>/` | `<suite>/profile/` | a file the user names, or none | `code_root` in `00-build.md` |
| inside a project | `<project>/planning/suite/build/` | `<project>/planning/build/` | `<project>/planning/profile/` | `<project>/planning/maquette/<x>/60-brief.md` | `<project>/` minus `planning/` |

```
<work>/
  00-build.md            Director    control — features, stage tables, code root, cross-feature interactions
  CONTEXT.md             concept     ubiquitous language of the whole project (seeded from the brief)
  decisions/             concept     one ADR per decided question: ADR-<n>-<slug>.md
  deployment.md          synthesis   register of deployment and support scripts
  test_tooling.md        synthesis   register of test and debug tooling
  <FEAT>/                one folder per feature
    10-intake.md         intake      Path B extract + Path A grilling → draft requirements
    20-concept.md        concept     Sections 0–12: requirements, ADR links, interactions, architecture, consistency matrix
    30-synthesis.md      synthesis   Sections 13–16: test concept, slice log, gap matrix, script registers (pointers)
    40-rescue.md         rescue      one file per rescue round: 40-rescue.md, 40-rescue.v2.md …
```

Product code never lives under `<work>`. `planning/maquette/<x>/vcode/` is read, never written (contract H2). The stage skills that write code (synthesis, rescue) write only under `code_root`.

## 3. Frontmatter

Every result file starts with:

```yaml
---
stage: <control|intake|concept|synthesis|rescue>
owner: <skill name>
status: <open|in_progress|done|skipped>
revision: <integer, starts at 1>
created: <ISO timestamp, minutes>
updated: <ISO timestamp, minutes>
input: <file>@<revision>[, <file>@<revision>]
feature: <FEAT>
---
```

`00-build.md` additionally carries `title`, `code_root`, `git`, `language`, `path` (A / B / mixed, per feature), `field` (field of application, per feature), `profile`, `contract_in`. Stage skills read those; only the Director writes them. `20-concept.md` carries `version` (the concept document version, bumped at every release — MANIFEST "Concept ↔ Code Synchronization").

## 4. Write rules

1. **One owner per file.** Write only files whose `owner` is your skill name. Read anything. If you believe another file is wrong, say so to the user and record it under "Open questions" in *your* file.
2. **Done is frozen.** Never modify a file whose `status` is `done`. A rerun ("redo") writes `NN-<stage>.v2.md` (then `.v3.md` …) with `revision: 1` in the new file. The Director records which version is current in `00-build.md`. For `20-concept.md` this is the MANIFEST rule "document first, code follows": a concept change after code exists is a new version of the document, committed before the code that implements it.
3. **Findings flow forward, never backward.** A requirement discovered in synthesis is appended to the current concept version through `redo` of the concept stage (new `.vN` file, IDs appended, never renumbered) — not silently edited into a done file, and never written back into the brief or `vcode/`.
4. **Optimistic locking.** Before writing: read the file, note `revision`. When writing: set `revision + 1` and `updated`. If the file's `revision` on disk is no longer what you read, write `NN-<stage>.conflict.md` with your content and tell the user in one sentence.
5. **Human notes are untouchable.** When rewriting a file, copy `## Notes (human)` verbatim. Never edit, reorder, summarise or delete it. If the human wrote something there that changes your work, treat it as user input — act on it in your sections, leave theirs alone.
6. **Cite your input.** `input` names the files and revisions you read. If the Director marks your file `stale` (the input changed), you do nothing until the user says "redo".
7. **Templates are the definition.** Fill the template from `templates/NN-<stage>.md`. Keep every section heading, in order. A section that does not apply gets the line "not relevant for this feature" — never delete it. Remove the `<!-- -->` guidance comments and the `<…>` placeholders in the finished file.
8. **Shared project files** (`CONTEXT.md`, `decisions/`, `deployment.md`, `test_tooling.md`) are append-and-revise registers, not frozen files: their owner stage may update them at any time, always with `revision + 1`, never deleting an entry — obsolete entries are marked `obsolete (reason)`.

## 5. Conversation rules

- **One question at a time.** Never stack questions. A grilling session is a sequence of single questions, batched by theme, not a questionnaire.
- **Language.** Skill text is English. Talk to the user in the language they use (German: informal "du"). Write result files in the language given by `language` in `00-build.md`; keep the template's section headings English. Requirement text, identifiers, code and commit messages are always English.
- **Opening line.** Each stage starts with two sentences: what this stage produces and roughly how long it takes. No lecture.
- **Evidence marks.** Facts the user gives are tagged `[evidenced]`, `[estimated]` or `[unknown]`. Never fill a gap with your own guess; `[unknown]` is a valid answer that becomes an open question `Q<n>`.
- **Requirements first, solutions later.** In intake and the first half of concept, push back when the conversation drifts to code.
- **Opportunity language.** Say "still open" / "to be clarified", never "bad" / "unrealistic".
- **Stop anytime.** On "stop" (or its equivalent): write the file with whatever exists, mark unfinished sections "open", set `status: in_progress`, hand back to the Director.
- **Time budget.** `budget_min` in `00-build.md` gives your minutes per stage; synthesis budgets are per slice. When you reach it, say so and offer to close with the current state. Do not silently run over.
- **Inline first, file last.** Show interim results as formatted text in the chat. Write the result file once at the end of the stage (or on stop) — not continuously. Exception: synthesis writes code and the slice log after every slice.
- **No confidentiality promises.** Never claim the platform keeps no logs.
- **No hidden actions.** Say what you are about to write or run before you do it. No commits, installs, network calls or file writes the user did not hear about.

## 6. Git

Git is optional and never created by a skill. `git` in `00-build.md` is `yes` only if `<work>` or `code_root` (or a parent) is a git repository at start; skills never run `git init`. With `git: no`, every commit step below is skipped and the result file records "— (no git)".

With `git: yes`, a commit marks a **frozen state, never progress**.

| Event | What is committed | Message |
| --- | --- | --- |
| a result file reaches `status: done` | that file only (Director) | `build(<FEAT>): <stage> done` |
| synthesis finishes a slice with tests green | code of the slice + `30-synthesis.md` + any register it touched | `build(<FEAT>): S<n> <slice name> — synthesized from 20-concept.md@<rev>` |
| a concept version is accepted after code exists | the new `20-concept.vN.md` + `00-build.md`, before the code that follows | `concept(<FEAT>): rev <N> — <reason>` |
| rescue applies a refactor | one commit per refactor; revert on regression | `build(<FEAT>): rescue <n> — <finding>` |
| a release point is reached | concept version bump + code | `build(<FEAT>): release <version>` |

Every commit that changes behaviour cites the requirement IDs it touches in the message body (`Traces to: F-<FEAT>-x, NF-<FEAT>-y`). Skills never push, rebase, branch or commit files outside `<work>` and `code_root`.

## 7. Handing back to the Director

At the end of a stage, the stage skill reports in one short block: file(s) written (name, revision), status (`done` proposed / `in_progress`), minutes used, open questions count, gate findings if any (Phase 6 / Phase 9 matrices). Only the Director sets `status: done` and updates the stage table in `00-build.md`.

## 8. Profile (reserved)

A workspace may contain a `profile/` folder with customer-specific constraints (allowed stacks, IT rules, mandatory standards, coding conventions, the customer's own review process). The Director reads it at start and passes the relevant parts to each stage. **A profile may restrict, never loosen:** write rules, git behaviour and "nothing outside the folder" stay as defined here. The profile format is defined in `profile/README.md` once the first profile exists.

## 9. Contracts

build is the last of three skillsets and talks to its predecessor through one file. A missing contract file is never an error — it only means more questions for the user.

- **H2 — in.** `60-brief.md` (contract `H2/1`, written by maquette-brief), Part 3 "Handover to build", plus the frozen `vcode/` next to it. Intake runs Path B on them first (extract what the prototype already answers), then Path A on the rest (grill what it does not). Neither file is ever written by build.
- **Without a brief:** cold start. Intake asks for the problem statement and runs Path A in full (MANIFEST Phase 1). `contract_in: none`.
- **Out:** none. The product and its concept documents are the result.

## 10. Working principles for anyone writing code here

Carried over from the former `CLAUDE.local.md` v2 (adapted from Karpathy's observations on LLM coding pitfalls). They bind synthesis and rescue, and any coding agent that reads `planning/build/` as its specification.

1. **Think before coding.** State assumptions explicitly; when several interpretations exist, present them — never silently pick one. Surface trade-offs; if a simpler approach exists, say so.
2. **Simplicity first.** The minimum code that satisfies the requirement — no speculative features, no abstractions for single-use code, no error handling for impossible cases. If 200 lines could be 50, rewrite. Less code is a reliability requirement, not a style preference.
3. **Surgical changes.** Touch only what the requirement needs; every changed line traces to a requirement ID. No drive-by refactors, no reformatting. Remove only the orphans your change created; mention pre-existing dead code, do not delete it unasked.
4. **Goal-driven execution.** Turn tasks into verifiable goals: "add validation" → "write tests for invalid inputs, then make them pass". For multi-step work, state a plan with an explicit check per step and finish with a verification step.
5. **Describe before changing.** When a change to an existing document or file is proposed, explain intent and wording first; change after approval.

## 11. Definition of Done (per feature)

A feature is done when: every behaviour traces to a numbered requirement; every requirement has a test and the test passes; `20-concept.md` and `30-synthesis.md` reflect the current behaviour (no drift); no orphan requirements and no orphan code remain; the Phase 9 Gap Analysis Matrix is clean or every open item carries a logged decision; every script and tool is registered (`deployment.md`, `test_tooling.md`); the concept document version is bumped at the release point. The litmus test: a fresh agent, given only `<work>`, can reproduce the current code.
