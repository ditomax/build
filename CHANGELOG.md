# Changelog — build

## 0.1.7 — 2026-09-17
README: new **Language** and **Git** sections (git moved out of *Inside a project*), intro language sentence matched to idea and maquette, section order aligned with idea and maquette. No contract change. The company abbreviation DMBG is gone from the public texts: the product is simply the skill suite (idea → maquette → build); source credits in `ATTRIBUTION.md` name Dietmar Millinger.

## 0.1.6 — 2026-09-17
Discoverability: README gains § The suite (links to idea, maquette, build and skill-suite-setup, one line each, pointer to the `planning/` form for multi-skillset or customer use); § For agents no longer tells agents to ignore skill-suite-setup — point 3 names it as the producer of the `planning/` form, point 4 keeps only `hooks/` and `guard.py` as developer-only. START calls skill-suite-setup "the suite's setup tool" instead of "the maintainers' tool". No contract change.

## 0.1.5 — 2026-09-16
Consistency fix: README's German-only language aside removed, its content folded into the English text (a customer-variant concern, not the base README's). No contract change.

## 0.1.4 — 2026-09-16
Onboarding: README § For agents, AGENTS.md guard, START fallback names the folder. Director: cold-start question offers the maquette folder path; a first message mentioning maquette/brief/prototype asks for the path first (D1' wording, ID unchanged); git `yes` only if the work folder is tracked (a clone counts as no); `examples/` with fictitious finished results (Example GmbH). ADRs are `ADR-<FEAT>-<nnn>-<slug>.md` (counter per feature); concept §8 may point to a requirement instead of an ADR for fact-settled questions; decided questions keep the brief's OQ numbers; checklist gets a closest-field hint. Director: `status: done` bumps revision (§4.4). No contract change.

## 0.1.3 — 2026-09-16
Documentation: prerequisites (development environment and shell for the product), chaining from maquette by naming the brief, profile pointer to skill-suite-setup/PROFILE.md with a minimal example, compatibility line, QUESTIONS.md in the structure, update and uninstall notes. No contract change.

## 0.1.2 — 2026-09-16
RULES §8 profile reading incl. `profile/questions.md`; Director reads it. QUESTIONS.md IDs declared stable.

## 0.1.1 — 2026-09-15
Reads contract H2/2 (all four brief parts; guardrails and deliberately-cannot rows in intake). Open questions `OQ-n`. `H2/1` briefs accepted with a note. QUESTIONS.md added.

## 0.1.0 — 2026-09-15
First version: MANIFEST.md (Concept Document Authoring Manifest v8), RULES with working principles and definition of done, checklists, Director + intake / concept / synthesis / rescue, templates for concept documents and registers.
