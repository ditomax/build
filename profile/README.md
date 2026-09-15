# profile/ — customer-specific constraints (reserved)

This folder is empty in the public release. When a build workspace is prepared for one customer, it holds that customer's constraints, and the Director reads it at every start (RULES §8).

A profile may **restrict, never loosen**: write rules, git behaviour and "nothing outside the work root" stay as defined in `RULES.md`. The profile shapes content and frame.

Planned files (the format is fixed with the first real profile):

| File | Read by | Purpose |
| --- | --- | --- |
| `profile.md` | Director | customer, default language, budgets, sponsor / product owner role |
| `standards.md` | intake, concept | mandatory standards and regulations beyond the checklists, house-specific compliance |
| `it-constraints.md` | concept, synthesis | allowed stacks and dependencies, hosting, data rules, "no network" |
| `conventions.md` | synthesis, rescue | coding conventions, repository layout, CI expectations |
| `review.md` | Director | who signs which gate, the customer's own review and release process |

Until a profile exists, this README is the only file here and means "core defaults".
