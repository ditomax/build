---
stage: shared
owner: build-synthesis
status: in_progress
revision: 1
created: <YYYY-MM-DDThh:mm>
updated: <YYYY-MM-DDThh:mm>
input: —
---

# Test & Debug Tooling Register

_Single inventory of every test runner, fixture generator, debug helper, inspection script and diagnostic command in this project. Read it **before** writing any such tool and reuse if one fits; register every new or changed tool in the **same commit**; remove or mark `obsolete (reason)` in the same commit a tool is deleted. The gap analysis (MANIFEST Phase 9) checks both directions: orphans and ghosts._

| Name | Purpose | Invocation (copy-paste) | Key parameters | Environment & preconditions | Docs | Status |
| --- | --- | --- | --- | --- | --- | --- |
| | | | | | | |

**Column guide** — Invocation: the exact command line from the repo root. Environment & preconditions: target env (dev/stage/prod), LLM or network calls (yes/no), required mocks, test data location, env vars/secrets. Status: `active` | `obsolete (reason)`.

## Notes (human)

<!-- Off limits for all skills. Copied verbatim on every rewrite. -->
