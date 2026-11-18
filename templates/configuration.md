---
stage: shared
owner: build-synthesis
status: in_progress
revision: 1
created: <YYYY-MM-DDThh:mm>
updated: <YYYY-MM-DDThh:mm>
input: —
---

# Configuration

_Every setting of the product: name, type, default, whether it is secret, what it controls. The single source of truth is in the code — `src/<pkg>/core/config.py` (Python profiles) or `config/keys.toml` (all others); the table between the markers is generated from it by `python tools/dev.py docs`, and `check` fails while it is out of date. Values come from the environment; a secret's value never appears here, in the code, in a commit or in a chat (RULES §10.6). Concept §10 holds the typed configuration structures; this file is their operational view._

## Settings

<!-- generated:start -->
<!-- generated:end -->

## Environments

Which settings differ per environment and where their values come from (never the values of secrets).

| Setting | dev | stage | prod | Source of the value (env file, vault, platform) |
| --- | --- | --- | --- | --- |
| | | | | |

## Notes (human)

<!-- Off limits for all skills. Copied verbatim on every rewrite. -->
