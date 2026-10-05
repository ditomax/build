# Changelog — build

## 0.1.10 — 2026-10-05
`ATTRIBUTION.md` names the AI tools used in development (Claude Opus, GPT-6 Astra).

Running cost and load become requirements. New intake question **I8** (on every agenda, whatever the path): what makes running cost grow with use, who pays, the heaviest user, a monthly ceiling — "fixed running cost only" closes it. New intake question **I9** (every path): users, concurrency at peak, peak pattern, data volume, external rate limits — at launch, in one and three years; "small and stable" with figures is a valid answer and the written reason to defer scale-out. `templates/10-intake.md` gains Part 2 § *Running cost* and § *Load and growth*; `templates/20-concept.md` gains §5 *Load and growth model* (single source of volume figures, scale decision) and §5 *Operating cost model* (no section renumbered). `checklists/requirements.md` Backend gains the row *Load and growth model*, and a new § *Capacity & Scaling (Software)* (connection-pool budget, indexed and bounded queries, retention, long work out of the request path, backoff with jitter, external rate limits, stateless instances and load shedding for scale-out, capacity test to the first bottleneck); `checklists/architecture-consistency.md` gains *Capacity budget*, *Request path*, *Instance-local state*; concept §11 gains *Scaling strategy* (capacity budget, first bottleneck, lever, trigger). `checklists/requirements.md` Version 2 gains § *Operating Cost (Software)* (cost model, heavy-user coverage, spending caps and budget guard, quotas, caching, token budget, cost observability); `checklists/architecture-consistency.md` Version 2 gains the row *Cost drivers*. Trigger: an article on AI-built MVPs whose variable cost outgrew a flat price. QUESTIONS.md Version 3. No contract change.

## 0.1.9 — 2026-09-25
Operation moves into intake: new question **I7** (on every agenda, whatever the path) — who operates the product permanently, per role (content / data upkeep, technology / hosting, user support) a name and a time share; unknown → `OQ-<n>`, nobody → sponsor before concept. `templates/10-intake.md` gains Part 2 § *Operation*. Counterpart of idea 0.2.4, where V6 now only settles a plausible operating model (asking for names at idea stage proved too early in a field test). QUESTIONS.md Version 2. No contract change.

## 0.1.8 — 2026-09-23
Additive, for distributed real-time systems (DRS): `checklists/requirements.md` gains § *Distributed real-time system (DRS)* (global time, temporal accuracy, permanence, transactions, fault hypothesis, replica determinism, membership, load hypothesis and thrashing, composability, gateways, end-to-end protocols, reintegration). `checklists/architecture-consistency.md` gains seven rows marked *(DRS)*. MANIFEST: one sentence reading the phases as a V-model, and in Phase 5 that a vertical slice in a DRS is a transaction. No phase renamed, no contract change.

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
