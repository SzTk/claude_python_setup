---
name: python-uv-workflow
description: Enforce uv-managed virtualenv usage for Python development in this repository.
---

This skill standardizes Python development around a local virtual environment
created and managed by `uv`.

## 1. When to use this skill

- Use this skill whenever the task touches Python code, Python tooling, or Python tests.
- Use it before running `python`, `pip`, `pytest`, `ruff`, `mypy`, build backends,
  or package installation commands.

## 2. Project root detection

- Search upwards from the current working directory for `pyproject.toml`.
- Treat the directory containing `pyproject.toml` as the Python project root.
- If no `pyproject.toml` is found, say that the repository does not expose a Python root
  and avoid guessing.

## 3. Environment bootstrap

- In the Python project root, prefer a local `.venv` directory.
- If `.venv` does not exist:
  - run `uv venv`
  - then run `uv sync`
- If `.venv` already exists:
  - still run project commands through `uv run ...`
  - run `uv sync` when dependencies may have changed or when a command is missing

## 4. Command policy

- Always prefer:
  - `uv run python`
  - `uv run pytest`
  - `uv run ruff`
  - `uv run mypy`
  - `uv run <tool>`
- Use `uv add <package>` for dependencies when the task requires adding Python packages.
- Use `uv remove <package>` when removing dependencies.
- Avoid global `pip install ...` and avoid assuming an already-activated shell environment.

## 5. Verification

- When checking the active interpreter, use:
  - `uv run python --version`
  - `uv run python -c "import sys; print(sys.executable)"`
- If a Python command fails, first verify:
  - the correct `pyproject.toml` root was selected,
  - `.venv` exists,
  - `uv sync` has been run,
  - the tool is present in project dependencies.

## 6. Collaboration guidance for older models

- Be explicit in plans and command choices that Python work must go through `uv`.
- Do not say "activate the environment" as the main path unless the user asks for that flow.
- Prefer command examples that are complete and copyable, such as `uv run pytest`.

## 7. Short rule for agents

- If the repository has `pyproject.toml`, Python work happens through `uv run ...`.
