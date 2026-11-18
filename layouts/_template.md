<!-- build layout catalogue — profile template (RULES §15). For a stack the catalogue does not cover: copy this file into the layout ADR, fill every section from the ecosystem's own conventions (official guide, standard build tool), mark it "unreviewed layout". Keep all seven headings; a section that does not apply says why. -->

# Layout profile `<name>` — <stack>

**Status:** unreviewed layout · **Sources:** <official guide / build tool documentation the layout follows>

## 1. Skeleton

```
<code_root>/
  AGENTS.md  CLAUDE.md  README.md
  <build manifest>
  planning/                 (RULES §2 — never inspected by the layout check)
  <source folders>
  <test folders>
  tools/                    dev.py, layout_check.py, secret_check.py
  config/keys.toml          (non-Python stacks)
```

## 2. Mapping

| build term | In this stack |
| --- | --- |
| feature | <the stack's unit: package, crate, target, module, …> at `<path>` |
| core | <shared kernel unit> — never depends on a feature |
| entry point / composition root | <where the program starts and features are wired> |
| public interface of a feature | <what other features may use: exported header, `pub` items, module API, …> |

## 3. Dependency rule and its check

Direction entry → features → core; another feature only through its public interface (L1, L2). Enforced by: <build system mechanism> — plus `tools/layout_check.py` for L4, L5.

## 4. Tests

| Kind | Location | Runner | One test |
| --- | --- | --- | --- |
| unit | <…> | <…> | `<command>` |
| integration | <…> | <…> | `<command>` |
| fixtures, golden outputs | <…> | — | — |

## 5. Execution surface

`tools/dev.py` PROFILE block:

```python
PKG = ""
TEST = [<test command>]
LAYOUT = [sys.executable, "tools/layout_check.py"]
LINT = [[<linter command>]]
TOOLS = [<tools doctor reports>]
REQUIRED = ["git", "<build manifest>"]
```

## 6. Hygiene additions

Append to `.gitignore` (after the H1 block of `templates/repo-hygiene.md`):

```gitignore
<build output folders, IDE files, local caches>
```

Secrets: <where the stack reads them at runtime — environment, platform keystore, …; never in the build manifest or a committed config file>.

## 7. Agent limits

<what cannot run in a typical Linux agent sandbox (toolchain, emulator, signing, hardware) and the command the user runs instead — synthesis records "not verified here — run `<command>` on <platform>">
