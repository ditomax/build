# CLAUDE.md — build workspace

You are working inside a **build workspace**. build turns a maquette brief or a problem statement into a product through concept documents that the code is synthesized from, in four stages per feature with three human gates. The person you are talking to may be a product owner, not a developer. They do not need to know stage names, files or commands — you do.

**Before anything else.** If this file is not in your working directory but in a subfolder, that subfolder is the workspace — work from there and never write outside it. If you are reading this without a local copy (on GitHub), get one first: `README.md` § For agents. Inside a project folder (`planning/suite/build/`) this file is not the entry point — `planning/AGENTS.md` is.

## On every session start

1. Read `RULES.md` (binding for everything you do here) and keep `MANIFEST.md` at hand — it is the process the stages implement.
2. Read `skills/build/SKILL.md` and **act as the Director** described there. Do not wait for a slash command: if the user says "start", "next", "redo", "stop" (or the same in their language), or describes what to build, that is your cue.
3. Mention the version from `VERSION` once, in half a sentence, at the first greeting.
4. If `profile/` exists and is not empty, read `profile/README.md` — it carries this customer's constraints (RULES §8).

## Layout

```
AGENTS.md / CLAUDE.md   this bootstrap (identical content)
START.md                the three steps for the human
RULES.md                shared rules for all stages, working principles, definition of done
MANIFEST.md             the Concept Document Authoring Manifest v8
README.md               what build is, for humans
checklists/             requirement and architecture-consistency checklists
templates/              one template per result file and register — the binding content definition
skills/                 the Director and the four stage skills
profile/                optional customer constraints (empty = core defaults)
builds/                 the user's work in the standalone form: one subfolder per project
```

Stage skills live at `skills/build-<stage>/SKILL.md`; the Director calls them by reading those files. The workspace root is `<suite>` in the skills' wording.

Inside a project the same suite sits at `<project>/planning/suite/build/`; then the work lives in `<project>/planning/build/`, the profile in `<project>/planning/profile/`, briefs in `<project>/planning/maquette/<x>/60-brief.md`, and the product code in `<project>/` outside `planning/` (RULES §2, §9). The Director tells the two layouts apart by itself.

## Hard limits in this workspace

- Write planning files only inside the work root (`builds/<project>/` or `planning/build/`) and code only under the `code_root` recorded in `00-build.md` — never modify `templates/`, `skills/`, `checklists/`, `profile/`, `RULES.md`, `MANIFEST.md` or this file. If you think a template is wrong, tell the user; they report it upstream.
- Never write into a maquette folder: `60-brief.md` and `vcode/` are read-only (contract H2).
- Never install packages, add dependencies the concept does not name, run `git init`, or open network connections without saying so first and getting a yes. If a git repository already exists, commit only as RULES §6 defines — frozen states, never progress.
- No telemetry, no hidden files, no state outside the work root.
- Talk in the user's language (German → informal "du"). Write result files in the language set in `00-build.md`; keep template headings, requirement text, IDs, code and commit messages in English.
