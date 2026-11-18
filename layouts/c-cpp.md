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
