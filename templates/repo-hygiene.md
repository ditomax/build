<!-- Template for synthesis slice 0 (repository hygiene) and rescue audit rounds — RULES §13. Copy every block verbatim; never retype it from memory. Not a result file: no frontmatter, nothing to fill in except where <…> says so. -->

# Repository hygiene — files and checks

Constraint SC-HYG: **no secret and no host-local agent file ever reaches git, an image or a permission allow list.** Losses L9 (and L3, L8). The files below are written into `code_root` at slice 0 (before S1) and re-checked in every rescue round. With `git: no` they are written anyway — a repository may come later.

Merge rule: a file that already exists is **appended to or merged**, never overwritten — missing lines are added, existing ones stay. Show the user what you add before you write it.

## H1 — `.gitignore` (R1)

Append this block if any of its lines is missing:

```gitignore
# --- skill suite: secret hygiene (build RULES §13) ---
.claude/settings.local.json
CLAUDE.local.md
.env
.env.*
!.env.example
secrets/
```

**Check** (read-only commands):

1. `git check-ignore -v .claude/settings.local.json CLAUDE.local.md .env .env.local` — every line must name `.gitignore` of this repository as the source, not a global file (`~/.config/git/ignore`, `core.excludesFile`).
2. `git ls-files .claude/settings.local.json CLAUDE.local.md .env 'secrets/*'` and `git ls-files | grep -E '(^|/)\.env(\.|$)' | grep -v '\.env\.example$'` — must print nothing. A tracked file stays tracked despite `.gitignore`: tell the user, who removes it with `git rm --cached <file>`; if it was ever pushed, the audit's order applies (rotate first).

## H2 — `.dockerignore` (R2) — only when a `Dockerfile` exists or is generated

```gitignore
# --- skill suite: secret hygiene (build RULES §13) ---
.git
.claude/
CLAUDE.local.md
.env
.env.*
!.env.example
secrets/
```

In the `Dockerfile`: no secret in `ARG` or `ENV` (both stay in the image history); secrets needed at build time come through BuildKit secrets (`RUN --mount=type=secret,id=<name> …`, `docker build --secret id=<name>,src=<file>`), secrets needed at run time through the runtime environment.

## H3 — `.claude/settings.json` (R3, R9) — committed; Claude Code only

Other hosts: skip H3 and say so in the slice log; H1, H2, H4 and H5 still apply. Merge into an existing file: add the `deny` rules and the hook, keep everything else.

```json
{
  "permissions": {
    "deny": [
      "Read(./.env)",
      "Read(./.env.*)",
      "Read(!.env.example)",
      "Read(./secrets/**)"
    ],
    "allow": [
      "Bash(pytest *)",
      "Bash(python -m pytest *)"
    ]
  },
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          { "type": "command", "command": "python3 \"$CLAUDE_PROJECT_DIR\"/tools/secret_check.py hook" }
        ]
      }
    ]
  }
}
```

Rules for this file: `allow` holds only generalized rules — program and subcommand, then `*` (`Bash(pytest *)`, `Bash(npm run *)`), never a full literal command line; no `env` block with credentials; nothing user-specific (that belongs in `.claude/settings.local.json`, which H1 ignores). The `deny` rules stop the agent's file tools from reading `.env` and `secrets/`; the program itself still loads `.env` at run time.

## H4 — secret rule for agents (R4)

Append to the project's `AGENTS.md` (and `CLAUDE.md`, if present) — never overwrite them:

```markdown
## Secrets (skill suite, build RULES §13)

- Never put a secret into a shell command, a commit message, a file under version control or a chat answer. Pass secrets as environment variables or keep them in `.env` (git-ignored), which the program loads (Python: `python-dotenv`); refer to them by name (`$DATABASE_URL`).
- When a permission prompt offers "don't ask again", prefer a generalized rule (`Bash(pytest *)`) over the literal command; never approve a command that contains a secret.
- Found a secret where it does not belong? Say so, never repeat the value, and treat it as compromised: rotate first, clean up second.
```

## H5 — `tools/secret_check.py` and its test (R9, R6)

The PreToolUse hook (H3) and the audit (A2) use the same script. Register both in `test_tooling.md` in the same step.

`tools/secret_check.py`:

```python
#!/usr/bin/env python3
"""secret_check — keep secrets out of agent commands and permission allow lists.

Written by the build skill (repository hygiene, slice 0). Standard library only.

  secret_check.py hook                 Claude Code PreToolUse hook: reads the tool call as JSON on stdin,
                                       exits 2 (blocks) if a Bash command carries a secret inline.
  secret_check.py scan <file>...       audit: checks permission allow lists in .claude/settings*.json for
                                       secrets and overly literal entries; exit 1 on a secret finding.

Never prints a secret value — only the file, the entry number and the pattern type.
"""
import json
import re
import sys

PATTERNS = [
    ("private key block", re.compile(r"-----BEGIN [A-Z ]*PRIVATE KEY-----")),
    ("AWS access key", re.compile(r"\b(AKIA|ASIA)[0-9A-Z]{16}\b")),
    ("GitHub token", re.compile(r"\b(gh[pousr]_[A-Za-z0-9]{30,}|github_pat_[A-Za-z0-9_]{40,})")),
    ("Anthropic / OpenAI key", re.compile(r"\bsk-(ant-)?[A-Za-z0-9_\-]{20,}")),
    ("Slack token", re.compile(r"\bxox[abposr]-[A-Za-z0-9\-]{10,}")),
    ("Google API key", re.compile(r"\bAIza[0-9A-Za-z_\-]{35}\b")),
    ("bearer token", re.compile(r"(?i)authorization:\s*bearer\s+[A-Za-z0-9._\-]{20,}")),
    ("credentials in URL", re.compile(r"[a-z][a-z0-9+.\-]*://[^\s:/@$]+:(?!\$)[^\s@/]{3,}@")),
    ("password option", re.compile(r"(?i)(--password[= ](?![\"']?\$)\S{4,}|\bmysql(dump|admin)?\b[^|;&]*\s-p(?!\s*\$)\S{4,})")),
    ("secret assignment", re.compile(
        r"(?i)\b[A-Z0-9_]*(passw(or)?d|secret|token|api[_-]?key|access[_-]?key)[A-Z0-9_]*\s*[=:]\s*['\"]?(?![$<{])[^\s'\"$]{8,}")),
]

GENERIC_HINT = ("Do not put secrets into commands. Pass them as environment variables or keep them in .env "
                "(loaded by the program, e.g. python-dotenv) and refer to them by name.")


def findings(text: str) -> list[str]:
    return [name for name, rx in PATTERNS if rx.search(text)]


def cmd_hook() -> int:
    try:
        event = json.load(sys.stdin)
    except (json.JSONDecodeError, ValueError):
        return 0  # not our business — never break the session on malformed input
    if event.get("tool_name") != "Bash":
        return 0
    command = (event.get("tool_input") or {}).get("command") or ""
    hits = findings(command)
    if not hits:
        return 0
    print(f"Blocked by secret_check: the command looks like it contains a secret ({', '.join(hits)}). "
          + GENERIC_HINT, file=sys.stderr)
    return 2


def literal(entry: str) -> bool:
    """An allow entry like Bash(psql -h db -U app mydb) — a full command line without a wildcard."""
    m = re.fullmatch(r"Bash\((.*)\)", entry.strip())
    return bool(m) and "*" not in m.group(1) and len(m.group(1).split()) >= 3


def cmd_scan(paths: list[str]) -> int:
    secret_found = False
    for path in paths:
        try:
            with open(path, encoding="utf-8") as fh:
                data = json.load(fh)
        except FileNotFoundError:
            continue
        except (json.JSONDecodeError, ValueError):
            print(f"{path}: not valid JSON — check by hand")
            continue
        perms = data.get("permissions") or {}
        for key in ("allow", "ask", "deny"):
            for i, entry in enumerate(perms.get(key) or [], start=1):
                hits = findings(str(entry))
                if hits:
                    secret_found = True
                    print(f"{path}: permissions.{key}[{i}] — secret pattern: {', '.join(hits)}")
                elif key == "allow" and literal(str(entry)):
                    print(f"{path}: permissions.allow[{i}] — overly literal; replace with a generalized rule "
                          "(program and subcommand, then *)")
        env = data.get("env") or {}
        for name in env:
            if findings(f"{name}={env[name]}"):
                secret_found = True
                print(f"{path}: env.{name} — secret pattern in a settings value")
    return 1 if secret_found else 0


def main() -> int:
    if len(sys.argv) >= 2 and sys.argv[1] == "hook":
        return cmd_hook()
    if len(sys.argv) >= 3 and sys.argv[1] == "scan":
        return cmd_scan(sys.argv[2:])
    print(__doc__, file=sys.stderr)
    return 64


if __name__ == "__main__":
    sys.exit(main())
```

`tests/test_secret_check.py` (fake secrets are assembled at runtime, so no secret-like literal is ever committed):

```python
"""Tests for secret_check.py. Fake secrets are assembled at runtime so no secret-like literal sits in the repo."""
import json
import subprocess
import sys
import tempfile
from pathlib import Path

SCRIPT = Path(__file__).resolve().parents[1] / "tools" / "secret_check.py"

AKIA = "AKIA" + "Q" * 16
GHP = "ghp_" + "a1B2" * 9
SK = "sk-" + "ant-" + "x" * 30
PEM = "-----BEGIN " + "RSA PRIVATE KEY-----"

BLOCK = [
    f"aws s3 ls --profile x AWS_ACCESS_KEY_ID={AKIA}",
    f"curl -H 'Authorization: Bearer {'z' * 40}' https://api.example.org",
    f"git clone https://user:{'p4ss' * 3}@github.com/org/repo.git",
    f"psql postgresql://app:{'S3cret' * 2}@db:5432/app",
    f"mysql -u root -p{'hunter2' * 2} app",
    f"pg_dump --password={'hunter2' * 2} app",
    f"export OPENAI_API_KEY={SK}",
    f"echo '{PEM}' > key.pem",
    f"gh auth login --with-token <<< {GHP}",
    f"DB_PASSWORD={'correcthorse' * 2} python manage.py migrate",
]
ALLOW = [
    "pytest -q",
    "export API_KEY=$API_KEY && python app.py",
    "psql \"$DATABASE_URL\"",
    "git clone https://github.com/org/repo.git",
    "python -c 'from dotenv import load_dotenv; load_dotenv()'",
    "TOKEN=${GITHUB_TOKEN} gh pr list",
    "grep -rn password src/",
    "docker build --secret id=npmrc,src=$HOME/.npmrc .",
    "ls -p",
    "docker run -p8080:80 app",
    "ssh -p 2222 host",
    "mysql --password=\"$DB_PW\" app",
    "git commit -m 'refresh the token on expiry'",
    "pip install -r requirements.txt",
]


def run_hook(cmd: str) -> subprocess.CompletedProcess:
    event = {"tool_name": "Bash", "tool_input": {"command": cmd}}
    return subprocess.run([sys.executable, str(SCRIPT), "hook"], input=json.dumps(event),
                          capture_output=True, text=True)


def test_blocks_inline_secrets():
    for cmd in BLOCK:
        r = run_hook(cmd)
        assert r.returncode == 2, cmd
        secret_parts = [AKIA, GHP, SK, "p4ssp4ss", "S3cret", "hunter2", "correcthorse", "z" * 40]
        assert not any(p in r.stderr for p in secret_parts), "value echoed"


def test_allows_references_and_ordinary_commands():
    for cmd in ALLOW:
        assert run_hook(cmd).returncode == 0, cmd


def test_other_tools_and_bad_input_pass():
    r = subprocess.run([sys.executable, str(SCRIPT), "hook"], input=json.dumps(
        {"tool_name": "Read", "tool_input": {"file_path": ".env"}}), capture_output=True, text=True)
    assert r.returncode == 0
    r = subprocess.run([sys.executable, str(SCRIPT), "hook"], input="not json", capture_output=True, text=True)
    assert r.returncode == 0


def test_scan_reports_without_values():
    with tempfile.TemporaryDirectory() as d:
        p = Path(d) / "settings.local.json"
        p.write_text(json.dumps({"permissions": {"allow": [
            "Bash(pytest *)",
            f"Bash(export OPENAI_API_KEY={SK})",
            "Bash(psql -h db -U app appdb)",
        ]}, "env": {"SERVICE_TOKEN": "t0k" * 6}}))
        r = subprocess.run([sys.executable, str(SCRIPT), "scan", str(p)], capture_output=True, text=True)
        assert r.returncode == 1
        assert "allow[2] — secret pattern" in r.stdout
        assert "allow[3] — overly literal" in r.stdout
        assert "env.SERVICE_TOKEN" in r.stdout
        assert "allow[1]" not in r.stdout
        assert SK not in r.stdout and "t0k" not in r.stdout


def test_scan_clean_file():
    with tempfile.TemporaryDirectory() as d:
        p = Path(d) / "settings.json"
        p.write_text(json.dumps({"permissions": {"allow": ["Bash(pytest *)"], "deny": ["Read(./.env)"]}}))
        r = subprocess.run([sys.executable, str(SCRIPT), "scan", str(p)], capture_output=True, text=True)
        assert r.returncode == 0 and r.stdout == ""
```

## H6 — `.pre-commit-config.yaml` (R5)

Installing pre-commit and the gitleaks hook is an install: ask first (question Y0). Declined → write the file anyway, record "pre-commit: declined" in `00-build.md` *Repository hygiene* — the constraint is then enforced by push protection (H7) and the audit only.

```yaml
# skill suite: secret hygiene (build RULES §13). Activate once per clone: pre-commit install
repos:
  - repo: https://github.com/gitleaks/gitleaks
    rev: v8.18.4            # update with: pre-commit autoupdate
    hooks:
      - id: gitleaks        # without a Go toolchain: id gitleaks-system with a locally installed gitleaks
  - repo: local
    hooks:
      - id: no-host-local-files
        name: host-local agent files and .env never committed
        language: fail
        entry: "Never commit host-local agent files or .env files (build RULES §13):"
        files: '(^|/)(\.claude/settings\.local\.json|CLAUDE\.local\.md|\.env(\..+)?)$'
        exclude: '(^|/)\.env\.example$'
```

Activation: `pip install pre-commit` (or the project's dev dependencies), then `pre-commit install`. **Each clone** must run `pre-commit install` once — the hook is not active in a fresh clone, which is why H7 exists.

## H7 — server-side protection (R7)

When `git remote -v` names GitHub, ask Y0G: "Are secret scanning and push protection enabled for this repository? (Settings → Code security; free for public repositories.)" Record the answer in `00-build.md` *Repository hygiene*. build never changes repository settings itself. Other hosting: the equivalent (GitLab: secret push protection / secret detection), same question. No remote yet → ask again at the release point.

## Audit round (rescue `kind: audit`, R6, R8)

Read-only; nothing is changed, deleted or rewritten. Scanning tools are installs — ask first.

- **A1 — history:** `gitleaks detect --source . --log-opts="--all" --redact --report-format json --report-path <outside the repo>` — all branches and all commits, including deleted files. Report rule, file, commit and line from the JSON; never the value (`--redact`).
- **A2 — allow lists:** `python tools/secret_check.py scan .claude/settings.json .claude/settings.local.json` — secret patterns and overly literal allow entries, by file and entry number. `~/.claude/` is outside the project: name it as a place to check, do not read it.
- **A3 — controls in place:** H1 checks (both), H2 (if Docker), H3 (Claude Code), H4, H5 test green, H6 file present and hook installed in this clone (`.git/hooks/pre-commit` exists), H7 answer.
- **A4 — report** in `40-rescue.md` § *Secret audit*: one row per finding — where (file, line or commit), pattern type, status. Never a value, not even partly. With any secret finding the report states the order: **1. rotate** the secret (it is compromised the moment it was committed or pushed), **2. then clean** history (`git filter-repo` or BFG, then force-push, then every clone re-cloned) — cleaning is the user's job, never the skill's; without rotation, cleaning is worthless.
