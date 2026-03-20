---
name: /lint
description: Run lint, formatting, and static checks for both Python and Rust, applying fixes where possible.
---

# Goal

Provide a single command to run all code quality checks
across the Python and Rust parts of the project.

# Python environment policy

- For Python work, always prefer the project virtual environment managed by `uv`.
- Detect the Python project root by searching upwards for `pyproject.toml`.
- In that root:
  - If `.venv` does not exist, run `uv venv` and then `uv sync`.
  - If `.venv` exists, still prefer `uv run ...` over calling global tools directly.
- Never run bare `python`, `pip`, `pytest`, `ruff`, or `mypy` for project tasks when `uv`
  is available. Use `uv run python`, `uv run pytest`, `uv run ruff`, and `uv run mypy`.

# Project detection

- Starting from the current directory, search upwards for `Cargo.toml`.
  The directory containing the first `Cargo.toml` is treated as the Rust project root.
  - Example: if `/Cargo.toml` is found, `/` is the Rust project root.
- Similarly, search upwards for `pyproject.toml` and treat its directory
  as the Python project root.

# Default behavior

When the user runs `/lint`, perform the following steps:

1. Python side (if `pyproject.toml` is found):
   - Ensure the `uv` virtual environment is ready according to the policy above.
   - Run `uv run ruff check . --fix`
   - Run `uv run ruff format .`
   - Run `uv run mypy`
2. Rust side (if `Cargo.toml` is found):
   - Run `cargo fmt`
   - Run `cargo clippy --all-targets --all-features -D warnings`

Summarize the results of each command and propose fixes
for remaining errors or warnings.

# Options

- `/lint python` : Only run the `uv`-managed Python checks.
- `/lint rust`   : Only run `cargo fmt` and `cargo clippy` for the Rust side.
