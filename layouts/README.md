# Layout catalogue

How the product repository is organised — one profile per stack (RULES §15). The concept author picks the profile in the first feature (question C8) and records it in the layout ADR; synthesis slice 0 (foundation) writes the skeleton and the checks. Every profile has the same eight sections (`_template.md`); shared tools are in `_tools.md`.

| Profile | For | Feature = | Dependency rule enforced by | Status |
| --- | --- | --- | --- | --- |
| [`py-lib`](py-lib.md) | Python package, service, CLI tool, batch job | package `src/<pkg>/<feat>/` | `tests/architecture/test_layout.py` | reviewed |
| [`py-web`](py-web.md) | Python backend with a web page (server-rendered or SPA) | package + router | layout test + OpenAPI contract test | reviewed |
| [`c-cpp`](c-cpp.md) | C or C++ application or library | CMake library target | CMake target links | first edition |
| [`rust`](rust.md) | Rust application or library | crate in a cargo workspace | Cargo dependency graph | first edition |
| [`ios`](ios.md) | iOS app | local Swift package | `Package.swift` dependencies | first edition |
| [`android`](android.md) | Android app | Gradle module `:feature:<x>` | Gradle module dependencies | first edition |

*first edition* — written from the ecosystem's documented conventions, not yet field-tested with build.

## Something else — the fallback ladder

1. **The profile prescribes it.** `profile/conventions.md` names a layout → that one (a profile restricts, never loosens).
2. **No catalogue match.** The concept author copies `_template.md`, fills it with the canonical layout of that ecosystem (its official guides, its standard build tool) and records it as the layout ADR, marked **unreviewed layout**. The PM gate shows the flag. RULES §15 L1–L7 apply in full.
3. **Several stacks in one product** (for example a Python backend and an Android app). One profile per part under `apps/<part>/` — each part keeps its own skeleton and checks — plus a top-level layout ADR on how the parts talk to each other (the API contract and where it lives).
4. **Existing code** (intake question I12). Its layout is adopted and documented as profile `adopted` (L7); restructuring only in a rescue round with its own ADR.

Good unreviewed layouts become catalogue entries after a field test — that is the suite developers' job, not the user's.

## What every profile shares

- `planning/` is one top-level folder of the repository, never inspected by a layout check (RULES §2).
- One dev CLI `tools/dev.py` (T2) as the single execution surface; agents run what it offers.
- `planning/build/agent-testing.md` (T3) and `planning/build/manual-tests.md` (T1) exist from slice 0 on.
- Repository hygiene (`templates/repo-hygiene.md`) plus the profile's ignore lines.
- Guideline L6: many small files, one responsibility each.
- §8 error convention: a failure is signalled so that the caller cannot mistake it for a result, the context travels with it, and it is caught only where its E row's action can be taken; invariant checks right before every side-effecting call (never a construct the build can strip); unexpected errors at a side-effecting call handled by one action-boundary helper that carries the action's standing row; a catch-all otherwise only at an entry point (RULES §16).
