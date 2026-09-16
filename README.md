# build

**From brief to product — concept documents as the source of truth, code as their derivative.**

Version: see `VERSION` · September 2026 · DMBG · https://github.com/ditomax/build

For users, `START.md` is enough — three steps, no installation. The skills reply in whatever language they are addressed in.

## For agents

If you are reading this from a link and have no local copy yet:

1. Get one — `git clone https://github.com/ditomax/build` or unzip the latest release from https://github.com/ditomax/build/releases — into a folder the user chooses (default: `./build` next to their other work). That download is the only network access; the "no network" rule in `AGENTS.md` applies from then on.
2. Work from inside that folder and read its `AGENTS.md`. Do not act as the Director from this README — the rules live in `RULES.md` and `skills/`.
3. Layout: standalone (this folder as is) unless the user has a project with a `planning/` folder — then the suite belongs at `planning/suite/build/` and `planning/AGENTS.md` is the entry point, not this file.
4. A clone gives updates via `git pull`; the user's work in `builds/` is ignored by git, so nothing of it is ever committed to a clone. `hooks/`, `guard.py` and `skill-suite-setup` are for skillset developers — ignore them.

## What is build?

build is the third and last skillset of the suite **idea → maquette → build**. Where maquette produces a clickable model, build produces the product — and it does so AI-first: the concept documents in `planning/build/` are the specification an agent synthesizes the code from, not a description written afterwards. If a concept document is incomplete, the code will be incomplete. There is no developer who "knows what was meant". That standard, and the process that reaches it, is the Concept Document Authoring Manifest (`MANIFEST.md`, version 8), which build turns into a guided, stage-by-stage skillset.

## How it works

The user types **start** and afterwards only **next**, **redo** or **stop**. A Director skill reads the work folder, says where things stand, calls the next stage, and asks the three gate questions at which a human decides:

| Stage | "Team member" | MANIFEST | Result | Typical duration |
| --- | --- | --- | --- | --- |
| 1 intake | intake analyst | Path B, Phase 1 | `10-intake.md` — what the prototype already answers, what had to be asked, the draft | 1 h with a brief |
| 2 concept | concept author | Phases 2–6 | `20-concept.md` — numbered requirements, ADRs, interactions, architecture in slices, consistency matrix; `CONTEXT.md`, `decisions/` | 2 sessions |
| — gate | sponsor | Phase 4 | PM review: requirements approved before any architecture | — |
| — gate | sponsor | Phase 6 | consistency review: findings accepted, fixed or deferred | — |
| 3 synthesis | build team | Phases 7–9 | `30-synthesis.md` + product code — test concept, red-green-refactor per slice, registers, gap analysis | per slice |
| — gate | sponsor | Phase 9 | gap analysis: implemented vs. specified, before release | — |
| 4 rescue | maintenance crew | Phase 10 | `40-rescue.md` — entropy removed, drift reported; on demand, repeatable | 1 h |

Finished results look like `examples/10-intake.md` and `examples/20-concept.md` (fictitious company). One feature = one concept document. The first feature is the scope of the brief; later features are added one at a time, each checked against the existing ones (interactions and interferences, cross-feature table in `00-build.md`). Requirement IDs carry the feature code (`F-PRC-12`) so two features never collide.

**Compatibility.** In: `60-brief.md` contract `H2/2` from maquette ≥ 0.5.0 (`H2/1` accepted with a note). Out: none. Version triples tested together: [skill-suite-setup/compat.md](https://github.com/ditomax/skill-suite-setup/blob/main/compat.md). Changes: `CHANGELOG.md`.

## Structure

```
build/
  START.md             three steps for the human
  AGENTS.md            entry point for Codex — turns the agent into the Director
  CLAUDE.md            the same for Claude
  VERSION
  README.md            this file
  RULES.md             shared rules — frontmatter, write rules, conversation rules, git, contracts, working principles, definition of done
  QUESTIONS.md         every question the skillset asks, with stable IDs — the tailoring surface for profiles
  CHANGELOG.md         what changed per version
  hooks/               pre-commit guard for development clones (see Release)
  MANIFEST.md          the Concept Document Authoring Manifest v8 — normative process reference
  ATTRIBUTION.md       where the method comes from
  checklists/          requirements.md (Phase 1, by field of application) · architecture-consistency.md (Phase 6)
  profile/             optional customer-specific constraints (empty = core defaults)
  examples/            fictitious finished results — an intake and a concept document (Example GmbH)
  builds/              the users' work in the standalone form, one subfolder per project (not in the repo)
  templates/           one template per result file and per shared register (binding content definition)
  skills/
    build/             Director
    build-intake/
    build-concept/
    build-synthesis/
    build-rescue/
```

## Contracts

- **In — H2.** `60-brief.md` (contract `H2/2`) written by maquette ≥ 0.5.0 — all four parts — plus the frozen `vcode/` next to it. Intake extracts first (Path B), grills the rest (Path A). Neither file is ever written by build.
- **Without a brief** build starts from a problem statement and runs the manifest's Path A in full.
- **Out.** None — the product and its concept documents are the result.

## Inside a project

The workspace above is the standalone form. Inside a project folder the same suite is copied to `planning/suite/build/` (read-only), the work lives in `planning/build/`, the profile in `planning/profile/`, briefs wait in `planning/maquette/<x>/60-brief.md`, and the product code lives in the project root outside `planning/`. The Director recognises the layout by the `planning/` folder. A coding agent working on the product reads `planning/build/` as its specification and treats `planning/idea/` and `planning/maquette/` as history.

Git is optional. Skills never create a repository; if one exists, a commit marks a frozen state — a stage done, a green slice, an accepted concept version, a kept refactor — and every commit that changes behaviour cites the requirement IDs it touches (`RULES.md` §6).

## Profiles

Customer-specific variants (restricted topics, IT constraints, standards, corporate design, the customer's own review process, questions skipped or added) do not fork this repo. They live in a `profile/` folder the Director reads at start; a profile may restrict, never loosen. The format is specified in [skill-suite-setup/PROFILE.md](https://github.com/ditomax/skill-suite-setup/blob/main/PROFILE.md); a minimal example is in `profile/README.md`. `QUESTIONS.md` lists every question the skillset asks, with stable IDs — read it before a session, and use the IDs in a profile to skip or add questions.

## Distribution and installation

Repo and ZIP share the same structure. Users download the release ZIP, unzip it, open the folder in their AI app and type "start" (`START.md`). `AGENTS.md` / `CLAUDE.md` are read automatically and turn the agent into the Director — nothing to install, no symlinks, no global skill folders, no network, no telemetry. Developers who want the skills globally can symlink `skills/build*` into `~/.codex/skills/` or `~/.claude/skills/`.

## Release

Development clones activate the customer-data guard once: `git config core.hooksPath hooks` (the hook calls `guard.py` from the sibling `skill-suite-setup` repo and blocks commits that carry customer markers). Release ZIPs are built with `skill-suite-setup/release.py`, which ships only git-tracked, allowlisted, guard-clean files.

Repository: https://github.com/ditomax/build — releases at https://github.com/ditomax/build/releases. New version: bump `VERSION`, tag `vX.Y.Z`, GitHub release with the folder attached as `build-vX.Y.Z.zip`.

## Origin

build is the Concept Document Authoring Manifest (DMBG, v1–v8, 2026) plus its companions — the requirement checklists, the architecture consistency checklist, the test tooling register and the day-to-day working rules formerly kept in `CLAUDE.local.md` — restructured into the Director-and-stages form shared with idea and maquette. The manifest text lives on unchanged as `MANIFEST.md`. The working principles in `RULES.md` §10 adapt Andrej Karpathy's observations on LLM coding pitfalls.

## License

© 2026 Dietmar Millinger, MIT License (`LICENSE`).
