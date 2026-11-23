<!-- build layout catalogue — shared tool blocks (RULES §15). Copy every block verbatim; never retype from memory. Edit only the CONFIG / PROFILE block at the top of each file, as the profile and the layout ADR say. -->

# Shared tools for all layout profiles

Slice 0 (foundation) writes these files into `code_root`. Each profile says which ones it uses and what goes into their CONFIG / PROFILE block. All of them need only the Python standard library (3.11+) — Python is the host-side tool language in every profile, also for C/C++, Rust, iOS and Android; the product itself does not depend on it.

| File | Used by | Purpose |
| --- | --- | --- |
| `tools/dev.py` | all profiles | the dev CLI (T2): `test`, `check`, `config show / validate`, `doctor`, `docs` |
| `tests/architecture/test_layout.py` | `py-lib`, `py-web` | layout check: top-level folders (L5), grab-bag names (L4), dependency direction (L1, L2) |
| `tools/layout_check.py` | `c-cpp`, `rust`, `ios`, `android` | layout check: L4, L5 — the build system enforces L1, L2 |
| `src/<pkg>/core/config.py` | `py-lib`, `py-web` | single source of truth for settings |
| `config/keys.toml` | `c-cpp`, `rust`, `ios`, `android` | single source of truth for settings (the product's own loader reads the same key names) |

`python tools/dev.py docs` regenerates the generated block of `planning/build/cli.md` and `planning/build/configuration.md`; `check` fails while they are out of date. Adding a command or a setting therefore always means: change the code, run `docs`, commit both.

## `tools/dev.py`

````python
"""dev — the project's development CLI: test, check, configuration, environment (build RULES §15, T2).

Standard library only (Python 3.11+). Every command an agent or developer needs for testing and configuration goes
through here, so `planning/build/agent-testing.md` can point to one place. Add commands as the project
needs them; run `python tools/dev.py docs` afterwards — `check` fails while the docs are out of date.
"""
from __future__ import annotations

import argparse
import importlib
import os
import shutil
import subprocess
import sys
import tomllib
from pathlib import Path
from types import SimpleNamespace

# ── PROFILE (from the layout profile and the layout ADR) ─────────────────────
PKG = "<pkg>"                                   # Python profiles: import package under src/; else ""
TEST = [sys.executable, "-m", "pytest"]        # test runner; extra arguments are appended
LAYOUT = [sys.executable, "-m", "unittest", "tests.architecture.test_layout"]   # layout check (RULES §15)
LINT = [["ruff", "check", "."]]                # linters; each is skipped when its tool is missing
TOOLS = ["uv", "pytest", "ruff", "gitleaks", "pre-commit", "git"]   # reported by doctor
REQUIRED = ["git", "pyproject.toml"]           # doctor fails when one of these is missing
# ─────────────────────────────────────────────────────────────────────────────

ROOT = Path(__file__).resolve().parents[1]
DOCS = ROOT / "planning" / "build"
START, END = "<!-- generated:start -->", "<!-- generated:end -->"
sys.path.insert(0, str(ROOT / "src"))


def _run(cmd: list[str]) -> int:
    print("$", " ".join(cmd), flush=True)
    return subprocess.call(cmd, cwd=ROOT)


def _keys():
    """The settings: from src/<PKG>/core/config.py (Python profiles) or config/keys.toml (all others)."""
    if PKG:
        return importlib.import_module(f"{PKG}.core.config").KEYS
    types = {"str": str, "int": int, "float": float, "bool": bool}
    data = tomllib.loads((ROOT / "config" / "keys.toml").read_text(encoding="utf-8"))
    return [SimpleNamespace(name=k["name"], type=types[k.get("type", "str")], default=k.get("default"),
                            secret=k.get("secret", False), doc=k.get("doc", "")) for k in data.get("key", [])]


# ── commands ─────────────────────────────────────────────────────────────────
def cmd_test(args) -> int:
    """Run the test suite; further arguments go to the test runner."""
    return _run([*TEST, *args.extra])


def cmd_check(args) -> int:
    """Layout, secret check, linters (when installed) and docs in sync. Exit 0 = clean."""
    rc = _run(LAYOUT)
    if (ROOT / "tools" / "secret_check.py").is_file():
        settings = [p for p in (".claude/settings.json", ".claude/settings.local.json") if (ROOT / p).is_file()]
        if settings:   # the checker's own tests run with `test`
            rc |= _run([sys.executable, "tools/secret_check.py", "scan", *settings])
    for cmd in LINT:
        if shutil.which(cmd[0]):
            rc |= _run(cmd)
        else:
            print(f"{cmd[0]} not installed — skipped")
    rc |= cmd_docs(argparse.Namespace(check=True))
    print("check:", "clean" if rc == 0 else "FINDINGS")
    return 1 if rc else 0


def cmd_config_show(args) -> int:
    """Show every setting: name, value (secrets masked), source."""
    for key in _keys():
        if key.name in os.environ:
            value, source = ("********" if key.secret else os.environ[key.name]), "env"
        elif key.default is not None:
            value, source = key.default, "default"
        else:
            value, source = "—", "MISSING"
        print(f"{key.name:<28} {value!s:<24} {source}")
    return 0


def cmd_config_validate(args) -> int:
    """Validate the configuration; names problems, never values. Exit 1 on a problem."""
    problems = []
    for key in _keys():
        raw = os.environ.get(key.name)
        if raw is None:
            if key.default is None:
                problems.append(f"{key.name}: required, not set")
            continue
        ok = raw.lower() in ("1", "0", "true", "false", "yes", "no", "on", "off") if key.type is bool \
            else _converts(key.type, raw)
        if not ok:
            problems.append(f"{key.name}: expected {key.type.__name__}")
    print("invalid: " + "; ".join(problems) if problems else "valid")
    return 1 if problems else 0


def _converts(kind, raw: str) -> bool:
    try:
        kind(raw)
        return True
    except ValueError:
        return False


def cmd_doctor(args) -> int:
    """Check the development environment: interpreter, tools, files. Never reads secret values."""
    rows = [("python", sys.version.split()[0], False)]
    for tool in TOOLS:
        rows.append((tool, "found" if shutil.which(tool) else "missing", tool in REQUIRED))
    for name in sorted({".gitignore", ".env", *[r for r in REQUIRED if "." in r]}):
        rows.append((name, "present" if (ROOT / name).exists() else "absent", name in REQUIRED))
    bad = 0
    for name, state, required in rows:
        flag = "!" if required and state in ("missing", "absent") else " "
        bad += flag == "!"
        print(f"{flag} {name:<16} {state}")
    return 1 if bad else 0


def cmd_docs(args) -> int:
    """Regenerate the generated blocks of planning/build/cli.md and configuration.md (--check: compare only)."""
    stale = []
    for name, text in (("cli.md", _cli_reference()), ("configuration.md", _config_reference())):
        path = DOCS / name
        if not path.is_file():
            print(f"{path.relative_to(ROOT)}: missing (create it from the build template)")
            stale.append(name)
            continue
        old = path.read_text(encoding="utf-8")
        if START not in old or END not in old:
            print(f"{name}: generated markers missing")
            stale.append(name)
            continue
        head, rest = old.split(START, 1)
        _, tail = rest.split(END, 1)
        new = f"{head}{START}\n{text}\n{END}{tail}"
        if new != old:
            stale.append(name)
            if not args.check:
                path.write_text(new, encoding="utf-8")
                print(f"{name}: updated")
    if args.check and stale:
        print(f"docs out of date: {', '.join(stale)} — run: python tools/dev.py docs")
        return 1
    return 0


# ── reference generators ─────────────────────────────────────────────────────
def _cli_reference() -> str:
    os.environ["COLUMNS"] = "100"                   # stable help layout
    parser, subparsers = build_parser()
    out = ["```text", parser.format_help().rstrip(), "```"]
    for name, sub in subparsers.items():
        out += ["", f"### `python tools/dev.py {name}`", "", "```text", sub.format_help().rstrip(), "```"]
    return "\n".join(out)


def _config_reference() -> str:
    out = ["| Key | Type | Default | Secret | Meaning |", "| --- | --- | --- | --- | --- |"]
    for k in _keys():
        default = "required" if k.default is None else f"`{k.default}`"
        out.append(f"| `{k.name}` | {k.type.__name__} | {default} | {'yes' if k.secret else 'no'} | {k.doc.replace('|', chr(92) + '|')} |")
    return "\n".join(out)


# ── parser ───────────────────────────────────────────────────────────────────
def build_parser():
    parser = argparse.ArgumentParser(prog="python tools/dev.py", description=__doc__.splitlines()[0])
    sub = parser.add_subparsers(dest="command", required=True, metavar="command")
    subs = {}

    p = sub.add_parser("test", help=cmd_test.__doc__,
                       epilog="Any further arguments are passed on to the test runner, e.g. -k slice1 -x")
    p.set_defaults(func=cmd_test)
    subs["test"] = p

    p = sub.add_parser("check", help=cmd_check.__doc__)
    p.set_defaults(func=cmd_check)
    subs["check"] = p

    p = sub.add_parser("config", help="show or validate the configuration")
    csub = p.add_subparsers(dest="action", required=True, metavar="action")
    cs = csub.add_parser("show", help=cmd_config_show.__doc__)
    cs.set_defaults(func=cmd_config_show)
    cv = csub.add_parser("validate", help=cmd_config_validate.__doc__)
    cv.set_defaults(func=cmd_config_validate)
    subs["config"] = p

    p = sub.add_parser("doctor", help=cmd_doctor.__doc__)
    p.set_defaults(func=cmd_doctor)
    subs["doctor"] = p

    p = sub.add_parser("docs", help=cmd_docs.__doc__)
    p.add_argument("--check", action="store_true", help="compare only; exit 1 when out of date")
    p.set_defaults(func=cmd_docs)
    subs["docs"] = p
    return parser, subs


def main(argv: list[str] | None = None) -> int:
    parser, _ = build_parser()
    args, extra = parser.parse_known_args(argv)
    if extra and args.command != "test":
        parser.error(f"unrecognized arguments: {' '.join(extra)}")
    args.extra = extra
    return args.func(args)


if __name__ == "__main__":
    sys.exit(main())
````

## `tests/architecture/test_layout.py` (Python profiles)

```python
"""Layout check — build RULES §15 (L1, L2, L4, L5). Standard library only; runs under pytest or unittest.

Edit the CONFIG block when the layout ADR changes — nothing else. A failing check is a finding:
fix the code, or change the layout ADR first and this block second (document first, code follows).
"""
import ast
import pathlib
import unittest

# ── CONFIG (from the layout ADR) ─────────────────────────────────────────────
PKG = "<pkg>"                                    # the import package under src/
CORE = "core"                                  # shared kernel: never imports a feature
ENTRY = {"app", "cli", "__main__"}             # composition root / entry points (may import anything)
ALLOWED_TOP = {"planning", "src", "tests", "tools", "scripts", "docs"}   # top-level folders (L5)
FORBIDDEN = {"utils", "helpers", "misc", "common", "stuff"}              # module and package names (L4)
# ─────────────────────────────────────────────────────────────────────────────

ROOT = pathlib.Path(__file__).resolve().parents[2]
SRC = ROOT / "src"


def _modules():
    """Yield (dotted module parts, path) for every .py file under src/<PKG>."""
    for path in sorted((SRC / PKG).rglob("*.py")):
        parts = list(path.relative_to(SRC).with_suffix("").parts)
        yield parts, path


def _imports(parts, path):
    """Yield the dotted target parts of every import of PKG in one module (relative imports resolved)."""
    package = parts[:-1]
    tree = ast.parse(path.read_text(encoding="utf-8"), filename=str(path))
    for node in ast.walk(tree):
        if isinstance(node, ast.Import):
            for alias in node.names:
                yield alias.name.split(".")
        elif isinstance(node, ast.ImportFrom):
            if node.level:
                base = package[: len(package) - (node.level - 1)]
                mod = base + (node.module.split(".") if node.module else [])
            else:
                mod = node.module.split(".") if node.module else []
            for alias in node.names:
                # `from pkg.feat import calc` targets the submodule pkg.feat.calc when it exists;
                # `from pkg.feat import name` (a name from feat/__init__.py) targets pkg.feat
                sub = mod + [alias.name]
                yield sub if _exists(sub) else mod


def _exists(parts):
    """True when the dotted parts name a module or package under src/."""
    base = SRC.joinpath(*parts)
    return base.with_suffix(".py").is_file() or (base / "__init__.py").is_file()


def _unit(parts):
    """The top-level unit of a module inside PKG: CORE, a feature name, an entry module, or '' for PKG itself."""
    if len(parts) < 2 or parts[1] == "__init__":
        return ""
    return parts[1]


class LayoutTest(unittest.TestCase):
    def test_top_level_folders(self):
        """L5 — a new top-level folder needs an ADR, then an entry in ALLOWED_TOP."""
        extra = sorted(
            p.name for p in ROOT.iterdir()
            if p.is_dir() and not p.name.startswith(".") and p.name not in ALLOWED_TOP
        )
        self.assertFalse(extra, f"top-level folders not in the layout ADR: {extra}")

    def test_forbidden_names(self):
        """L4 — no grab-bag modules; prefix with the domain instead."""
        bad = [".".join(p) for p, _ in _modules() if set(p[1:]) & FORBIDDEN]
        self.assertFalse(bad, f"grab-bag names (use a domain name): {bad}")

    def test_dependency_direction(self):
        """L1, L2 — core imports no feature; a feature uses another only through its public interface."""
        found = []
        for parts, path in _modules():
            src_unit = _unit(parts)
            if src_unit in ENTRY or src_unit == "":
                continue
            for target in _imports(parts, path):
                if not target or target[0] != PKG or len(target) < 2:
                    continue
                dst_unit = target[1]
                if dst_unit in ("", src_unit, "__init__"):
                    continue
                where = f"{'.'.join(parts)} -> {'.'.join(target)}"
                if src_unit == CORE and dst_unit != CORE:
                    found.append(f"core imports a feature or entry: {where}")
                elif dst_unit in ENTRY:
                    found.append(f"feature imports an entry point: {where}")
                elif dst_unit != CORE and len(target) > 2:
                    found.append(f"deep import into another feature (use its __init__): {where}")
        self.assertFalse(found, "\n".join(found))


if __name__ == "__main__":
    unittest.main()
```

## `tools/layout_check.py` (all other profiles)

```python
"""Layout check for non-Python profiles — build RULES §15 (L4, L5). Standard library only.

The dependency direction (L1, L2) is enforced by the build system of the profile (CMake targets,
Cargo, SwiftPM, Gradle); this script checks what the build system cannot see. Edit CONFIG only.
Exit 0 = clean, 1 = findings.
"""
import pathlib
import sys

# ── CONFIG (from the layout ADR) ─────────────────────────────────────────────
ALLOWED_TOP = {"planning", "src", "include", "tests", "tools", "scripts", "docs", "config", "cmake"}
SOURCE_DIRS = ["src", "include"]                       # where source files live
SUFFIXES = {".c", ".cc", ".cpp", ".h", ".hpp", ".rs", ".swift", ".kt", ".java"}
FORBIDDEN = {"utils", "helpers", "misc", "common", "stuff"}  # file and folder names (L4)
IGNORE = {"build", "target", ".build", "out"}          # build output folders (git-ignored)
# ─────────────────────────────────────────────────────────────────────────────

ROOT = pathlib.Path(__file__).resolve().parents[1]


def main() -> int:
    findings = []
    for p in sorted(ROOT.iterdir()):
        if p.is_dir() and not p.name.startswith(".") and p.name not in ALLOWED_TOP | IGNORE:
            findings.append(f"L5 top-level folder not in the layout ADR: {p.name}")
    for top in SOURCE_DIRS:
        for p in sorted((ROOT / top).rglob("*")) if (ROOT / top).is_dir() else []:
            if IGNORE & set(p.relative_to(ROOT).parts):
                continue
            stem = p.stem.lower() if p.suffix in SUFFIXES else p.name.lower() if p.is_dir() else ""
            if stem in FORBIDDEN:
                findings.append(f"L4 grab-bag name (use a domain name): {p.relative_to(ROOT)}")
    for line in findings:
        print(line)
    print("layout:", "clean" if not findings else f"{len(findings)} finding(s)")
    return 1 if findings else 0


if __name__ == "__main__":
    sys.exit(main())
```

## `src/<pkg>/core/config.py` (Python profiles)

```python
"""Configuration — the single source of truth for every setting (build RULES §15, layout profile py-*).

Every setting is one Key. Values come from the environment; a git-ignored `.env` is loaded first when
python-dotenv is installed (RULES §10.6). Secret values are never printed, logged or written anywhere.
`tools/dev.py config show|validate` and `planning/build/configuration.md` are generated from KEYS.
"""
from __future__ import annotations

import os
from dataclasses import dataclass


@dataclass(frozen=True)
class Key:
    name: str                 # environment variable name, UPPER_SNAKE
    type: type                # str | int | float | bool
    default: object = None    # None = required
    secret: bool = False      # never printed, logged or committed
    doc: str = ""             # one line: what it controls, unit, valid range


KEYS: list[Key] = [
    Key("APP_LOG_LEVEL", str, "INFO", doc="log level: DEBUG | INFO | WARNING | ERROR"),
    # Key("APP_API_TOKEN", str, secret=True, doc="token for <service>; from the vault, never inline"),
]


class ConfigError(ValueError):
    """A required key is missing or a value has the wrong type."""


def _convert(key: Key, raw: str) -> object:
    if key.type is bool:
        if raw.lower() in ("1", "true", "yes", "on"):
            return True
        if raw.lower() in ("0", "false", "no", "off"):
            return False
        raise ConfigError(f"{key.name}: expected a boolean")
    try:
        return key.type(raw)
    except ValueError as exc:
        raise ConfigError(f"{key.name}: expected {key.type.__name__}") from exc


def load(env: dict[str, str] | None = None) -> dict[str, object]:
    """Read every key; raise ConfigError naming all problems (names only, never values)."""
    if env is None:
        try:
            from dotenv import load_dotenv  # optional dependency
            load_dotenv()
        except ImportError:
            pass
        env = dict(os.environ)
    values, problems = {}, []
    for key in KEYS:
        if key.name in env:
            try:
                values[key.name] = _convert(key, env[key.name])
            except ConfigError as exc:
                problems.append(str(exc))
        elif key.default is None:
            problems.append(f"{key.name}: required, not set")
        else:
            values[key.name] = key.default
    if problems:
        raise ConfigError("; ".join(problems))
    return values
```

## `config/keys.toml` (all other profiles)

```toml
# Single source of truth for every setting (build RULES §15). Values come from the environment;
# secret values are never printed, logged or committed. tools/dev.py reads this file; the product's
# own configuration loader uses exactly these names — a unit test in the product language checks that.
[[key]]
name = "APP_LOG_LEVEL"
type = "str"            # str | int | float | bool
default = "INFO"        # omit for a required key
doc = "log level: DEBUG | INFO | WARNING | ERROR"

# [[key]]
# name = "APP_API_TOKEN"
# secret = true
# doc = "token for <service>; from the vault, never inline"
```
