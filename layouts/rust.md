<!-- build layout catalogue — profile rust (RULES §15). First edition. Copy blocks verbatim; replace <pkg> (lower_snake) and <feat>. -->

# Layout profile `rust` — Rust application or library

**Status:** first edition · **Sources:** The Cargo Book (workspaces, package layout, tests), Rust API guidelines.

## 1. Skeleton

```
<code_root>/
  AGENTS.md  CLAUDE.md  README.md
  Cargo.toml                workspace: members, shared dependency versions, lints
  Cargo.lock                committed
  rust-toolchain.toml       pinned toolchain
  planning/
  crates/
    <pkg>-core/             config, errors, domain types used by ≥ 2 features
      Cargo.toml  src/lib.rs
    <pkg>-<feat>/           one crate per build feature
      Cargo.toml
      src/lib.rs            public interface: what is `pub` here
      src/<module>.rs       one responsibility each
      tests/<slice>.rs      integration tests of this crate
    <pkg>/                  the binary: src/main.rs — composition root
  tests/fixtures/           shared synthetic data, golden outputs
  config/keys.toml          settings (layouts/_tools.md)
  tools/                    dev.py, layout_check.py, secret_check.py
  scripts/
```

Small products may start as one crate with one module folder per feature (`src/<feat>/mod.rs`); the layout ADR says so, and the split into crates follows when a second deployable or a compile-time boundary is needed.

Workspace `Cargo.toml`:

```toml
[workspace]
resolver = "2"
members = ["crates/*"]

[workspace.package]
edition = "2021"
version = "0.1.0"

[workspace.dependencies]
<pkg>-core = { path = "crates/<pkg>-core" }

[workspace.lints.rust]
unsafe_code = "forbid"        # loosen only with an ADR

[workspace.lints.clippy]
unwrap_used = "warn"          # error convention §8 — denied by -D warnings in LINT
expect_used = "warn"
```

## 2. Mapping

| build term | In this profile |
| --- | --- |
| feature | crate `crates/<pkg>-<feat>/` (or module `src/<feat>/` in the one-crate variant) |
| core | crate `<pkg>-core` — depends on no feature crate |
| entry point / composition root | binary crate `crates/<pkg>/` |
| public interface of a feature | its `pub` items in `lib.rs` |
| module (concept §11) | one `.rs` file with one responsibility |

## 3. Dependency rule and its check

Every allowed dependency is one line in a crate's `[dependencies]`; Cargo refuses anything else and forbids cycles. That list equals the concept's §11 dependency picture — reviewed at Phase 6. `tools/layout_check.py` checks L4, L5 (`ALLOWED_TOP` with `crates`, `config`; `SOURCE_DIRS = ["crates"]`; `IGNORE` with `target`).

## 4. Tests

| Kind | Location | Runner | One test |
| --- | --- | --- | --- |
| unit | `#[cfg(test)] mod tests` in the module file (Rust convention) | cargo test | `python tools/dev.py test -p <pkg>-<feat> <name>` |
| integration | `crates/<pkg>-<feat>/tests/<slice>.rs` | cargo test | `python tools/dev.py test --test <slice>` |
| fixtures, golden outputs | `tests/fixtures/` | — | registered in `agent-testing.md` |

## 5. Execution surface

`tools/dev.py` PROFILE block:

```python
PKG = ""
TEST = ["cargo", "test"]                    # at the workspace root: all members
LAYOUT = [sys.executable, "tools/layout_check.py"]
LINT = [["cargo", "clippy", "--workspace", "--all-targets", "--", "-D", "warnings"], ["cargo", "fmt", "--check"]]
TOOLS = ["cargo", "rustc", "clippy-driver", "gitleaks", "pre-commit", "git"]
REQUIRED = ["git", "cargo", "Cargo.toml"]
```

`cargo xtask` is the idiomatic Rust alternative to `tools/dev.py`; the catalogue keeps `dev.py` so every profile has the same commands and the same generated `cli.md`.

## 6. Hygiene additions

```gitignore
# --- layout rust ---
/target/
**/*.rs.bk
```

Secrets: environment variables read by the product's config loader, names from `config/keys.toml` (a unit test in `<pkg>-core` checks the loader's key list against that file); never in `Cargo.toml` or `build.rs`.

## 7. Agent limits

None typical where rustup and cargo exist. Fetching crates is network access — say so first. Cross compilation and embedded targets: as `c-cpp`.

## 8. Error convention

How failures are signalled, propagated and caught (RULES §16). Every handling site carries out an E row of concept §11.

- **`Result<T, E>` everywhere a call can fail.** One error enum per crate (`thiserror`), named in `CONTEXT.md` terms; `anyhow` only in the binary's entry point. `?` propagates; library errors are wrapped with `#[from]` or `map_err` at the crate boundary.
- **No panics as error handling.** `unwrap()` and `expect()` only in tests and at proven invariants with a comment; a panic is a bug, not a reaction.
- **Context travels.** Error variants carry what the detector knows (item, step, E-ID) as fields; the handler logs them.
- **Caught where the action is.** A `match` on an error takes the row's action only where it can be taken; elsewhere the error propagates. The handling site names the row: `// E-<FEAT>-3`.
- **Check:** clippy `unwrap_used` and `expect_used` in `[workspace.lints.clippy]` (skeleton above), denied by `-D warnings` in `LINT`; every crate inherits them with `[lints] workspace = true`. Tests may allow them with `#[allow(clippy::unwrap_used)]`.
- **Invariant checks before every side effect** return `Err(Error::InvariantViolated { .. })` — never `debug_assert!`, which release builds strip; an invariant check is an ordinary detector with its own matrix row. Each side-effecting call goes through one action-boundary helper that maps any error (and a caught panic, `catch_unwind`) to the action's standing row (concept §11 reaction matrix); the entry point keeps a backstop for everything outside, and a panic hook there stores the context.
