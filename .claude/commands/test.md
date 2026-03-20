---
name: /test
description: Run lint first, then execute Python and Rust test suites.
---

# Goal

Treat lint plus tests as a single quality gate so that only code
that passes all checks gets committed.

# Python environment policy

- For Python work, always use the project virtual environment managed by `uv`.
- Detect the Python project root by searching upwards for `pyproject.toml`.
- In that root:
  - If `.venv` does not exist, run `uv venv` and then `uv sync`.
  - If `.venv` exists, still prefer `uv run ...` over calling tools directly.
- Never run bare `python`, `pip`, or `pytest` for project tasks when `uv` is available.

# Project detection

- Use the same upward search for `Cargo.toml` and `pyproject.toml` as `/lint`
  to determine the Rust and Python project roots.
- Run `uv run pytest` in the Python project root.
- Run `cargo test` in the Rust project root.

# Default behavior

When the user runs `/test`, perform the following steps:

1. Run the equivalent of the `/lint` command.
   - If there are fatal lint errors, report them and stop without running tests.
2. Python tests:
   - Ensure the `uv` virtual environment is ready according to the policy above.
   - Run `uv run pytest` in the Python project root.
3. Rust tests:
   - Run `cargo test` in the Rust project root.

If any test fails, analyze the logs and summarize:
- which step failed (lint / pytest / cargo test),
- the main error messages,
- suggested fixes.

# Options

- `/test python` :
  - Run `/lint python`, then only run `uv run pytest`.
- `/test rust` :
  - Run `/lint rust`, then only run `cargo test`.
- `/test fast` :
  - Run full lint.
  - Run tests while skipping slow or long-running tests
    (e.g. using markers or separate test suites).
