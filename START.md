# build — Start in three steps

build turns a clickable model (or a well-described problem) into a real product — and it does it by writing the specification first, so precisely that the AI can build the software from it and a second AI could build it again. You answer questions and make three decisions along the way; the AI writes the documents and the code and stores everything as readable text files.

**This folder contains only text files. No programs, no installation, no internet access.**

## 1. Put the folder somewhere

Unzip the file and place the `build` folder where you will find it again. Do not rename it, do not delete anything inside. If you come from maquette: inside a project the folder belongs at `planning/suite/build/`, next to the other two.

## 2. Open the folder in your AI app

- **ChatGPT app (Codex):** choose Codex mode → "Open project" → select this folder (or the project folder that contains `planning/`).
- **Claude (Cowork / Claude Code):** connect the folder and start.

The app reads the rules from this folder automatically.

## 3. Type "start"

Type **start** — or describe the problem in two sentences. From then on the AI guides you through four stations and asks after each one: **next**, **redo** or **stop**. Three times along the way it will ask you — or the person who owns the product — for a decision before it continues:

| Station | Who | What you get | Your decision |
| --- | --- | --- | --- |
| 1 | Intake analyst | Everything the prototype already answers, and the questions that remain | — |
| 2 | Concept author | Numbered requirements with reasons, then the architecture in slices | **Review the requirements** before architecture starts; accept or fix the consistency findings |
| 3 | Build team | The product, one slice at a time, with tests | **Accept the gap analysis** before release |
| 4 | Maintenance crew | A cleaner codebase, on demand | — |

You can stop at any time. Whatever exists by then stays in the folder and continues next time with **next**.

## Where is what?

Inside a project everything lives in `planning/build/`: one subfolder per feature (`PRC/10-intake.md`, `20-concept.md`, `30-synthesis.md`), the shared dictionary `CONTEXT.md`, the decisions in `decisions/`, and two registers (`deployment.md`, `test_tooling.md`). The product code lives outside `planning/`. Standalone, the same lives in `builds/<project>/`. At the end of every file there is a section **Notes (human)**: leave your own remarks there, the AI never touches it.

## If something does not work

- The AI does not react to "start"? Type: "Read AGENTS.md and begin."
- You want to add a second feature? Type: "New feature." The first one is kept.
- New version of build? Download the latest ZIP from https://github.com/ditomax/build/releases and replace the folder; your work is elsewhere and stays.

Version: see file `VERSION`. Questions and feedback: dietmar@millinger.at
