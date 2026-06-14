---
name: ship-check
description: Audit a Python script or small project for production-readiness. Use when the user asks "is this ready to ship?", wants a pre-release review, or wants to know what to fix before sharing/selling/deploying their code. Produces a scored report with prioritized fixes.
---

# Ship-Check: Production-Readiness Audit

Audit the target script/project and produce a scored report. Do NOT fix anything in this skill — only diagnose and prioritize. (Fixes are handled by the companion skills: harden-errors, add-logging, make-cli, add-tests, package-it, write-readme.)

## Process

1. Read every `.py` file in scope plus any `pyproject.toml`, `requirements*.txt`, `README*`.
2. Score each category below (0–2 points each, 20 max). Be strict: "partially" = 1, not 2.
3. Output the report in the exact format at the bottom.

## Categories and what to actually check

### 1. Error handling (0–2)
- 2: I/O, network, parsing, and subprocess calls wrapped with *specific* exceptions; errors produce actionable messages; nonzero exit codes on failure.
- 0: bare `except:`, `except Exception: pass`, or crashes with raw tracebacks on bad input.
- Look for: `open()` without try/FileNotFoundError, `requests.*` without Timeout/ConnectionError handling, `json.loads` without JSONDecodeError, `int(input())` patterns.

### 2. Secrets & config (0–2)
- 2: no literals that look like keys/tokens/passwords; config via env vars or a config file; sane defaults.
- 0: hardcoded paths like `/Users/<name>/...`, API keys, passwords, or URLs with embedded credentials.
- Grep for: `api_key =`, `password =`, `token =`, `sk-`, `Bearer `, absolute home paths.

### 3. Inputs & validation (0–2)
- 2: every external input (args, files, env, network) validated with a clear error on bad input.
- Check: what happens with an empty file? a missing column? a 10 GB file? a path with spaces?

### 4. Logging & observability (0–2)
- 2: `logging` module with levels, not `print()` for diagnostics; user-facing output separated from debug noise.
- 0: print-debugging everywhere or total silence on failure.

### 5. Tests (0–2)
- 2: pytest suite covering the happy path AND at least 3 failure modes; runs green.
- 1: some tests exist but skip failure modes.
- 0: no tests. (Most scripts. Say so plainly.)

### 6. Dependency hygiene (0–2)
- 2: dependencies declared with version bounds in `pyproject.toml` or `requirements.txt`; no unused imports; stdlib preferred where reasonable.
- Check: does the import list match the declared deps? Are deps pinned too tightly (`==`) or not at all?

### 7. Interface & UX (0–2)
- 2: proper CLI (`argparse`/`--help`/exit codes) or documented function API; progress feedback on long operations.
- 0: hardcoded values you must edit in source to use it.

### 8. Packaging & install (0–2)
- 2: installable (`pip install .` works), entry point defined, runs from any directory.
- 0: "clone it and run python script.py and hope."

### 9. Documentation (0–2)
- 2: README with: what it does (1 sentence), install, a copy-pasteable usage example, expected output, troubleshooting.
- 1: README exists but missing a runnable example.

### 10. Portability (0–2)
- 2: no OS-specific paths/commands without fallbacks; Python version stated; works from a fresh venv.
- Check: `os.system` with shell-specific commands, hardcoded `\\` or `/` paths, locale/encoding assumptions (`open()` without `encoding=`).

## Report format (use exactly this)

```
SHIP-CHECK REPORT — <project name>
Score: <n>/20  →  <16+ "Shippable" | 11–15 "Close — fix the P1s" | ≤10 "Not yet — needs a hardening pass">

P1 (blocks shipping):
- <finding> — <file:line> — fix with: <companion skill or one-line fix>

P2 (fix before charging money for it):
- ...

P3 (nice to have):
- ...

Suggested order: <which companion skills to run, in order>
```

## Rules
- Cite file:line for every finding. No vague findings ("improve error handling" is banned; "open() at ingest.py:42 crashes on missing file" is correct).
- If the project scores 16+, say so and stop recommending work. Do not invent issues to seem thorough.
