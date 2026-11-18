<!-- build layout catalogue — profile py-web (RULES §15). Builds on py-lib: everything there applies unless this file says otherwise. Copy blocks verbatim; replace <pkg> and <feat>. -->

# Layout profile `py-web` — Python backend with a web page

**Status:** reviewed · **Sources:** as `py-lib`; the web framework's project guide (FastAPI, Flask, Django …); OpenAPI 3.1 for the API contract.

Two variants — the layout ADR names one:

- **W1 server-rendered** — templates and static files inside the package; one deployable, no JavaScript build. Default for internal tools and small user counts.
- **W2 separate front end** — a single-page app in `web/` with its own `package.json`, talking to the backend only through the API contract. For rich interaction or a separate front-end team.

## 1. Skeleton

```
<code_root>/
  AGENTS.md  CLAUDE.md  README.md
  pyproject.toml  uv.lock  .env.example
  planning/
  contracts/
    openapi.json            the API contract — generated from the backend, committed, diffed in tests
  src/<pkg>/
    __init__.py
    app.py                  composition root: creates the web app, mounts every feature's router
    core/                   config.py, errors.py, db / session handling, auth primitives
    <feat>/
      __init__.py           public interface: router + names other features may use
      routes.py             HTTP layer only: parse, call, respond — no business logic
      service.py            the feature's logic (more files as it grows, one responsibility each)
      store.py              persistence of this feature (when it has any)
      schemas.py            request / response models
      templates/<feat>/     W1 only: this feature's templates
    static/                 W1 only: shared CSS, JS, images
  web/                      W2 only: the SPA (own skeleton, see below)
  tests/
    architecture/test_layout.py
    architecture/test_contract.py   the backend's OpenAPI equals contracts/openapi.json
    <feat>/  integration/  fixtures/
    e2e/                    browser tests (Playwright), one file per click path
  tools/  scripts/
```

W2 `web/` follows its framework's own layout, organised by feature as well:

```
web/
  package.json  package-lock.json  tsconfig.json
  src/
    api/                    client generated from contracts/openapi.json — never hand-edited
    features/<feat>/        components, hooks, styles of one feature
    shared/                 design tokens, layout, generic components
    main.ts
  tests/
```

`ALLOWED_TOP` in `test_layout.py`: add `contracts` (W1, W2) and `web` (W2).

## 2. Mapping

| build term | In this profile |
| --- | --- |
| feature | package `src/<pkg>/<feat>/` (+ `web/src/features/<feat>/` in W2) |
| core | `src/<pkg>/core/` (+ `web/src/shared/` in W2) |
| entry point / composition root | `src/<pkg>/app.py` — the only place that knows all routers |
| public interface of a feature | its router and the names in `__init__.py`; across the network: the paths in `contracts/openapi.json` |
| interface table (concept §11) | one row per API path, with its requirement IDs |

## 3. Dependency rule and its check

As `py-lib`, plus: `routes.py` calls `service.py`, never `store.py` of another feature; the front end talks to the backend only through `web/src/api/` (W2). Checks: `test_layout.py` (L1, L2, L4, L5) and `test_contract.py` — the API contract is a *contract*: a change is a concept change first (interface table), then `python tools/dev.py contract` regenerates `contracts/openapi.json`, then the code.

`tests/architecture/test_contract.py` (FastAPI shown; for another framework, replace the line marked `framework`):

```python
"""The served API equals the committed contract (concept §11 interface table)."""
import json
import pathlib
import sys
import unittest

ROOT = pathlib.Path(__file__).resolve().parents[2]
CONTRACT = ROOT / "contracts" / "openapi.json"
sys.path.insert(0, str(ROOT / "src"))


def current_spec() -> dict:
    from <pkg>.app import create_app        # framework: FastAPI app factory
    return create_app().openapi()


class ContractTest(unittest.TestCase):
    def test_contract_up_to_date(self):
        committed = json.loads(CONTRACT.read_text(encoding="utf-8"))
        self.assertEqual(committed, current_spec(),
                         "API differs from contracts/openapi.json — concept first, then: python tools/dev.py contract")
```

Add to `tools/dev.py` (a command and its parser entry, then `python tools/dev.py docs`):

```python
def cmd_contract(args) -> int:
    """Regenerate contracts/openapi.json from the backend (after the concept's interface table changed)."""
    import json
    sys.path.insert(0, str(ROOT))
    from tests.architecture.test_contract import CONTRACT, current_spec
    CONTRACT.parent.mkdir(exist_ok=True)
    CONTRACT.write_text(json.dumps(current_spec(), indent=2, sort_keys=True) + "\n", encoding="utf-8")
    print(f"written: {CONTRACT.relative_to(ROOT)}")
    return 0

# in build_parser():
#     p = sub.add_parser("contract", help=cmd_contract.__doc__)
#     p.set_defaults(func=cmd_contract)
#     subs["contract"] = p
```

## 4. Tests

| Kind | Location | Runner | One test |
| --- | --- | --- | --- |
| unit | `tests/<feat>/test_<module>.py` | pytest | `python tools/dev.py test tests/<feat>/test_service.py::test_<name>` |
| API / integration | `tests/integration/test_<slice>.py` with the framework's test client | pytest | `python tools/dev.py test -k <slice>` |
| contract | `tests/architecture/test_contract.py` | pytest | `python tools/dev.py check` |
| browser (e2e) | `tests/e2e/test_<click path>.py` | pytest + Playwright | `python tools/dev.py test tests/e2e -k <path>` |
| W2 front-end unit | `web/tests/` | the framework's runner (Vitest …) | `npm --prefix web test -- <name>` |
| manual | `planning/build/manual-tests.md` | people | look and feel, real devices, accessibility checks with assistive technology |

## 5. Execution surface

`tools/dev.py` PROFILE block:

```python
PKG = "<pkg>"
TEST = [sys.executable, "-m", "pytest"]
LAYOUT = [sys.executable, "-m", "unittest", "tests.architecture.test_layout", "tests.architecture.test_contract"]
LINT = [["ruff", "check", "."]]               # W2: add ["npm", "--prefix", "web", "run", "lint"]
TOOLS = ["uv", "pytest", "ruff", "playwright", "node", "npm", "gitleaks", "pre-commit", "git"]
REQUIRED = ["git", "pyproject.toml"]
```

## 6. Hygiene additions

The `py-lib` block, plus:

```gitignore
# --- layout py-web ---
node_modules/
/web/dist/
/web/.vite/
/test-results/
/playwright-report/
*.sqlite3
```

Secrets: as `py-lib`. Never in templates, static files or the front-end bundle — anything shipped to the browser is public. Session and signing keys come from the environment.

## 7. Agent limits

Browser tests need Playwright with a browser; when it is missing, synthesis records "not verified here — run `python tools/dev.py test tests/e2e` locally". `npm install` and `playwright install` are installs (Y0).
