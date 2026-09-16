# build — Start in three steps

build turns a clickable model (or a well-described problem) into a real product — and it does it by writing the specification first, so precisely that the AI can build the software from it and a second AI could build it again. You answer questions and make three decisions along the way; the AI writes the documents and the code and stores everything as readable text files.

**This folder contains only text files.** Nothing is installed.

## What you need

- An AI app that can **read and write files in this folder**: ChatGPT app in Codex mode, Claude (Cowork, with the folder connected), Claude Code or Codex in a terminal, Mistral Vibe CLI. A plain chat window without folder access is not enough.
- A development environment for the product you are building — the AI writes real code and tests next to `planning/`, so it needs a shell it is allowed to use, and your product needs whatever your product needs (language runtime, packages). The AI names every dependency before installing anything and asks first.
- The skillset itself installs nothing and needs no internet access. Your product is a different matter: it is software, and it will need what software needs.

## 1. Put the folder somewhere

Unzip the file and place the `build` folder where you will find it again. You may rename the folder; keep everything inside it.

## 2. Open the folder in your AI app

- **ChatGPT app (Codex):** choose Codex mode → "Open project" → select this folder.
- **Claude (Cowork):** new task → connect folder → select this folder.
- **Claude Code / Codex / Vibe in a terminal:** change into the folder and start the program.

The app is meant to read the rules from this folder by itself. **If nothing happens after step 3, type: "Read AGENTS.md and begin."**

## 3. Type "start"

Type **start** — or describe the problem in two sentences. From then on the AI guides you through four stations and asks after each one: **next**, **redo** or **stop**. Three times along the way it will ask you — or the person who owns the product — for a decision before it continues:

| Station | Who | What you get | Your decision |
| --- | --- | --- | --- |
| 1 | Intake analyst | Everything the prototype already answers, and the questions that remain | — |
| 2 | Concept author | Numbered requirements with reasons, then the architecture in slices | **Review the requirements** before architecture starts; accept or fix the consistency findings |
| 3 | Build team | The product, one slice at a time, with tests | **Accept the gap analysis** before release |
| 4 | Maintenance crew | A cleaner codebase, on demand | — |

You can stop at any time. Whatever exists by then stays in the folder and continues next time with **next**.

## Coming from maquette? (chaining)

If a clickable model exists, its `60-brief.md` is the starting point: type "start", and when the AI asks what to build, say *"here is the brief:"* and give the path to the maquette folder (the brief and the model's `vcode/` next to it). The AI extracts what the model already answers and asks only about the rest. Without a brief, the problem in two sentences is enough — expect more questions then.

One project folder for all three tools — `planning/` with the skillsets, a profile and an entry point that knows which tool is up — is produced with the maintainers' tool [skill-suite-setup](https://github.com/ditomax/skill-suite-setup). That is also the recommended form for build, because the product code lives next to `planning/`.

## Where is what?

Inside a project everything lives in `planning/build/`: one subfolder per feature (`PRC/10-intake.md`, `20-concept.md`, `30-synthesis.md`), the shared dictionary `CONTEXT.md`, the decisions in `decisions/`, and two registers (`deployment.md`, `test_tooling.md`). The product code lives outside `planning/`. Standalone, the same lives in `builds/<project>/`. At the end of every file there is a section **Notes (human)**: leave your own remarks there, the AI never touches it.

## If something does not work

- The AI does not react to "start"? Type: "Read AGENTS.md and begin."
- You want to add a second feature? Type: "New feature." The first one is kept.
- New version of build? Download the latest ZIP from https://github.com/ditomax/build/releases, unzip it next to the old folder, and move your work folder (`builds/` and `profile/` if you have one) across. Work started under an older version is fine — the AI notices what changed and offers to redo a step where needed; it never fails on it. Ask us before editing a profile, so your changes survive the next version.
- Uninstall? Delete the folder. Your work is in the work folder — take it with you first.

Version: see file `VERSION`. Questions and feedback: dietmar@millinger.at
