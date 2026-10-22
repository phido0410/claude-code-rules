# Python Guardrail Setup

Use only for a large task in a project meant to last: a git repo with `pyproject.toml`/`setup.py`, or more than ~10 Python modules. Never for notebooks, one-off scripts, or repos the user doesn't own.

## When there is no check command

Ask once per project (if declined, don't ask again there): "This project has no check command. Set up ruff + pytest guardrails?" On yes:

1. Detect the package manager (`uv.lock` → uv, `poetry.lock` → poetry, else pip). Add tools as **dev dependencies only**:
   - Always: `ruff`, `pytest`
   - Multiple packages/layers or >~20 modules: `mypy`, `import-linter`
   - Non-trivial logic (parsing, math, data transforms): `hypothesis`
2. Config in `pyproject.toml`:
   - ruff: `select = ["E", "F", "B", "SIM", "ERA", "C90"]`, `max-complexity = 10`
   - import-linter: a `layers` contract matching the existing structure. Show the proposed layers to the user and get approval before writing it.
3. `Makefile`:
   ```makefile
   check:
   	ruff check .
   	pytest -q
   ```
   Append `mypy .` and `lint-imports` if installed.
4. Run `make check` once and report the baseline. Don't fix pre-existing violations in the same task; list them.

## After setup

Main CLAUDE.md §2 applies: never loosen these to pass. If a rule is genuinely wrong, propose the change and wait for approval.

Non-Python projects: propose the ecosystem equivalent (linter + type check + tests) the same way.
