---
name: /lint
description: Run lint, formatting, and static checks for both Python and Rust, applying fixes where possible.
---

# Goal

Provide a single command to run all code quality checks
across the Python and Rust parts of the project.

# Project detection

- Starting from the current directory, search upwards for `Cargo.toml`.
  The directory containing the first `Cargo.toml` is treated as the Rust project root.
  - Example: if `/Cargo.toml` is found, `/` is the Rust project root.
- Similarly, search upwards for `pyproject.toml` and treat its directory
  as the Python project root.

# Default behavior

When the user runs `/lint`, perform the following steps:

1. Python side (if `pyproject.toml` is found):
   - Run `ruff check . --fix`
   - Run `ruff format .`
   - Run `mypy`
2. Rust side (if `Cargo.toml` is found):
   - Run `cargo fmt`
   - Run `cargo clippy --all-targets --all-features -D warnings`

Summarize the results of each command and propose fixes
for remaining errors or warnings.

# Options

- `/lint python` : Only run ruff and mypy for the Python side.
- `/lint rust`   : Only run `cargo fmt` and `cargo clippy` for the Rust side.
