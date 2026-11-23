# Attribution

build is an original skill suite by Dietmar Millinger. It contains no code and no text copied from third-party projects. This file names the sources of its method.

## Concept Document Authoring Manifest — Dietmar Millinger, 2026

`MANIFEST.md` is version 8 of Dietmar Millinger's own manifest, developed across several projects in 2026 (versions 1–8), together with its companion files `requirement_checklists.md` (v1), `architecture_consistency_checklist.md` (v1), `test_tooling.md` and `CLAUDE.local.md` (v2). build restructures them into the Director-and-stages form shared with the idea and maquette skillsets; the manifest text itself is carried unchanged in substance.

## Andrej Karpathy — observations on LLM coding pitfalls

The four working principles in `RULES.md` §10 (think before coding, simplicity first, surgical changes, goal-driven execution) are adapted from the guidance collected at https://github.com/multica-ai/andrej-karpathy-skills. Method was adopted, not text.

## Nancy Leveson — STAMP / STPA (inspired by)

The STPA-light in build — losses, hazards, constraints, the control structure, unsafe control actions of four types, causal factors, and CAST-style incident analysis in rescue — is inspired by the work of Nancy Leveson: *Engineering a Safer World* (MIT Press, 2011), the *STPA Handbook* (with John Thomas, 2018), and STPA-Sec (with William Young, 2014). build takes the core mechanisms, not the full method, and aims them at reliability, security and robustness, not safety. Method was adopted, not text.

## Error handling strategy — Dietmar Millinger, lecture material

The four-step error handling strategy in `checklists/error-handling.md` (actions at system level first, detectable errors with their root causes, a reaction matrix with time limits, indication and storage) and the reaction-matrix form come from Dietmar Millinger's lecture material on distributed real-time systems, generalised for software products and first applied with build in a field test in November 2026. The fault / error / failure terms and the fail-silent and fail-stop failure modes follow the established dependability vocabulary (Avižienis, Laprie, Randell and Landwehr, 2004; Kopetz, *Real-Time Systems*). Reading error handling as the runtime control structure that answers STPA's loss scenarios (causal paths of two types, as in the handbook), the two-part constraint (keep out · response or mitigation — build's own convention, not an STPA definition) and the design order of answers (eliminate, prevent, detect and control, limit the damage) follow Leveson and Thomas, *STPA Handbook*, and the standard hierarchy of safety measures; no text is copied. The split between remedies by people and immediate actions by the product follows the same lecture's distinction between immediate system reactions and maintenance.

## Public AI security sources

`checklists/ai-threats.md` is built only from public material: NIST AI 100-2 E2025, the OWASP Top 10 for LLM Applications 2025 and for Agentic Applications 2026, MITRE ATLAS and ETSI EN 304 223. Threats are named and paraphrased in our own words; no text is copied.

## gstack — Garry Tan, MIT License

build itself adopts no gstack method directly. Its sibling maquette does (see that repo's `ATTRIBUTION.md`); the one-commit-per-fix, revert-on-regression discipline in the rescue stage reached build through maquette-harden.

## The suite

idea, maquette and build share the Director pattern, the frontmatter and write rules, the conversation rules and the git policy, first written for maquette 0.1–0.4 and generalised in the suite's target picture (September 2026).

## AI tools used in development

build was written and revised with the help of AI assistants: **Claude Opus** (Anthropic) and **GPT-6 Astra** (OpenAI). They drafted and edited texts and were used in field tests. The method, every decision and the review of every change are Dietmar Millinger's.
