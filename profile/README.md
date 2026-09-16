# profile/ — customer-specific constraints

Empty in the public release. A customer version carries this customer's constraints here; the Director reads `profile.md` at every start (RULES §8). The format is owned by the setup skill — see https://github.com/ditomax/skill-suite-setup/blob/main/PROFILE.md. This skillset reads: `profile.md`, `questions.md`, `standards.md` (mandatory norms and house checklist rows → intake, concept), `it-constraints.md` (stacks, hosting, network → concept, synthesis), `conventions.md` (coding conventions, CI, commit rules → synthesis, rescue), `review.md` (who signs which gate → Director).

A profile may **restrict, never loosen**. `questions.md` may skip (with a value) or add questions by the IDs in `QUESTIONS.md`.

## Minimal example

Put this into `profile/profile.md` and the Director will use it at the next start:

```markdown
# Profile — Example GmbH

- **Customer:** Example GmbH
- **Org code:** EXG
- **Document language:** de
- **Contact for questions:** Jane Doe, jane@example.com
- **Profile files present:** none
```

Every further file (`questions.md`, `scope.md`, …) is optional; the field spec is in PROFILE.md linked above.
