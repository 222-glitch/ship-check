# ship-check — free Claude Code skill

A scored, 10-category production-readiness audit for Python scripts. Findings
must cite `file:line` — vague advice is banned by the skill itself.

## Install (30 seconds)

```bash
git clone https://github.com/222-glitch/ship-check
cp -r ship-check/ship-check ~/.claude/skills/
```

Then in any Claude Code session:

```
Is this script ready to ship?
```

You get a scored report (0–20 across error handling, secrets, validation,
logging, tests, dependencies, CLI, packaging, docs, portability) with
prioritized P1/P2/P3 findings and the exact fix for each.

## Example output

```
SHIP-CHECK REPORT — expense_report.py
Score: 6/20  →  Not yet — needs a hardening pass

P1 (blocks shipping):
- bare except at line 15 swallows all errors then continues — produces wrong
  reports on partial reads
- hardcoded /Users/me/... path at line 4 — fails on every other machine
...
```

## ship-check tells you what's wrong. Fixing it is the rest of the kit.

The full **Ship-It Kit for Python** _(launching this week — link coming)_ is 8 skills covering
the whole last mile: pytest generation for existing code, error hardening,
print→logging, argparse CLI conversion, packaging (`pip install .` from a fresh
venv), README generation, and a release gate with secret scan — plus templates
and a verified before/after sample project.

## License

ship-check (this skill) is MIT — use it anywhere.
