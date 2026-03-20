---
name: /python-env
description: Prepare and verify the local Python development environment with uv.
---

# Goal

Make Python development deterministic by forcing project commands
to run inside a `uv`-managed virtual environment.

# Default behavior

When the user runs `/python-env`, perform the following steps:

1. Search upwards for `pyproject.toml`.
2. Treat that directory as the Python project root.
3. In that root:
   - If `.venv` does not exist, run `uv venv`.
   - Run `uv sync`.
4. Verify the environment by running:
   - `uv run python --version`
   - `uv run python -c "import sys; print(sys.executable)"`
5. Report:
   - the detected project root,
   - whether `.venv` was created or reused,
   - the Python executable path used by `uv run`.

# Enforcement policy

- For all subsequent Python development work in this repository, prefer:
  - `uv run python`
  - `uv run pytest`
  - `uv run ruff`
  - `uv run mypy`
- Avoid bare `python`, `pip`, `pytest`, `ruff`, and `mypy` unless the user explicitly asks.

# Options

- `/python-env sync` :
  - Run `uv sync` in the detected Python project root.
- `/python-env recreate` :
  - Recreate the environment with `uv venv` and then `uv sync`.
- `/python-env info` :
  - Only report the detected root, `.venv` status, and `uv run python` executable.
