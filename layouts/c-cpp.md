<!-- build layout catalogue — profile c-cpp (RULES §15). First edition. Copy blocks verbatim; replace <pkg> (project name, lower_snake) and <feat>. -->

# Layout profile `c-cpp` — C or C++ application or library

**Status:** first edition · **Sources:** CMake documentation (target-based usage requirements, `CMakePresets.json`), the Pitchfork layout conventions, GoogleTest / Catch2 with CTest.

## 1. Skeleton

```
<code_root>/
  AGENTS.md  CLAUDE.md  README.md
  CMakeLists.txt            top level: project(), options, add_subdirectory() per feature, tests
  CMakePresets.json         dev / release / test presets — the build commands agents use
  planning/
  include/<pkg>/<feat>/     public headers of a feature — the only headers other features may include
  src/
    core/                   CMakeLists.txt + sources of the core library (config, errors, logging)
    <feat>/                 CMakeLists.txt + sources and private headers of one feature
    app/                    main.cpp — composition root (executable target)
  tests/
    CMakeLists.txt
    <feat>/test_<module>.cpp
    integration/test_<slice>.cpp
    fixtures/
  cmake/                    helper modules (warnings, sanitizers, dependency setup)
  config/keys.toml          settings (layouts/_tools.md)
  tools/                    dev.py, layout_check.py, secret_check.py
  scripts/
```

Per feature, `src/<feat>/CMakeLists.txt`:

```cmake
add_library(<pkg>_<feat> <module>.cpp)
target_include_directories(<pkg>_<feat>
    PUBLIC  ${PROJECT_SOURCE_DIR}/include      # public headers: include/<pkg>/<feat>/…
    PRIVATE ${CMAKE_CURRENT_SOURCE_DIR})       # private headers stay invisible to other features
target_link_libraries(<pkg>_<feat> PUBLIC <pkg>_core)   # + the features it may use (concept §11)
target_compile_features(<pkg>_<feat> PUBLIC cxx_std_20)
```

## 2. Mapping

| build term | In this profile |
| --- | --- |
| feature | CMake library target `<pkg>_<feat>`: sources in `src/<feat>/`, public headers in `include/<pkg>/<feat>/` |
| core | target `<pkg>_core` — links no feature |
| entry point / composition root | executable target in `src/app/` — the only target that links all features |
| public interface of a feature | its headers under `include/<pkg>/<feat>/` |
| module (concept §11) | one header/source pair with one responsibility |

## 3. Dependency rule and its check

Every allowed dependency is one `target_link_libraries` line, and that list equals the concept's §11 dependency picture — review it at the Phase 6 consistency review. Private headers are not on another feature's include path, so a forbidden include fails the build. `tools/layout_check.py` checks L4 and L5 (`ALLOWED_TOP` with `include`, `cmake`, `config`). Recommended: warnings as errors in the `dev` preset; sanitizers (address, undefined) in the `test` preset.

## 4. Tests

| Kind | Location | Runner | One test |
| --- | --- | --- | --- |
| unit | `tests/<feat>/test_<module>.cpp` | GoogleTest or Catch2 via CTest | `python tools/dev.py test -R <name>` |
| integration | `tests/integration/test_<slice>.cpp` | CTest | `python tools/dev.py test -R <slice>` |
| fixtures, golden outputs | `tests/fixtures/` | — | registered in `agent-testing.md` |
| on target hardware | `planning/build/manual-tests.md` | people | MT rows with the hardware and firmware version in the execution log |

The test framework is a dependency (Y2): fetched with CMake `FetchContent` or from the system / vcpkg / Conan — the layout ADR names which.

## 5. Execution surface

`tools/dev.py` PROFILE block:

```python
PKG = ""
TEST = ["ctest", "--preset", "test", "--output-on-failure"]
LAYOUT = [sys.executable, "tools/layout_check.py"]
LINT = [["cmake", "--build", "--preset", "dev"]]      # + clang-tidy when the layout ADR adopts it
TOOLS = ["cmake", "ninja", "ctest", "clang-format", "clang-tidy", "gitleaks", "pre-commit", "git"]
REQUIRED = ["git", "cmake", "CMakeLists.txt"]
```

`check` builds the `dev` preset (warnings as errors) — a compile is the strongest layout check this profile has. Configure once with `cmake --preset dev`.

## 6. Hygiene additions

```gitignore
# --- layout c-cpp ---
# anchored with a leading slash: a bare "build/" would also ignore planning/build/
/build/
/out/
/cmake-build-*/
CMakeUserPresets.json
compile_commands.json
*.o
*.obj
*.a
*.so
*.dylib
*.dll
*.exe
.cache/
```

Secrets: environment variables read by the product's config loader, names from `config/keys.toml`; never compiled into the binary, never in `CMakePresets.json`.

## 7. Agent limits

A native compiler and CMake usually exist in Linux sandboxes. Cross toolchains, embedded targets, debuggers on hardware and Windows-only builds do not — synthesis records "not verified here — run `<command>` on <platform>", and a slice is green only after the user reports the result. Embedded products: check the scope boundary first (RULES §12).

## 8. Error convention

How failures are signalled, propagated and caught (RULES §16). Every handling site carries out an E row of concept §11. The choice below is recorded in the layout ADR — one per product, never mixed per module.

- **C:** functions return a status (`enum <pkg>_status`, `<PKG>_OK = 0`) and deliver results through out-parameters; never a result value that doubles as an error marker. Every status is checked: `__attribute__((warn_unused_result))` (or `[[nodiscard]]` in C23) on every function that can fail, warnings as errors in the `dev` preset. `errno` is copied right after the failing call.
- **C++:** `std::expected<T, Error>` (C++23, or a small equivalent in `core/`) — or exceptions with one hierarchy in `core/errors` — and `[[nodiscard]]` on every function that can fail. No `catch (...) {}`.
- **Context travels.** The error value carries what the detector knows (where, which item, the E-ID); the handler logs it with the core logging.
- **Caught where the action is.** An error is handled only where its row's action can be taken; elsewhere it is returned upward unchanged. The handling site names the row in a comment: `/* E-<FEAT>-3 */`.
- **CPU and hardware exceptions** (signals, faults, brown-out): a top-level handler records the last known context and enters the row's safe state — it cannot rely on the context of the error, so the detector writes that context beforehand.
- **Invariant checks before every side effect** return an error status (`<PKG>_INVARIANT`) or throw the product's error — never `assert()`, which `NDEBUG` compiles out; an invariant check is an ordinary detector with its own matrix row. Each side-effecting call goes through one action-boundary wrapper that maps any unexpected error to the action's standing row (concept §11 reaction matrix); besides it, the only catch-all is at an entry point.
