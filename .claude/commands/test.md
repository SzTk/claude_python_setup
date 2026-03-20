---
name: /test
description: Run lint first, then execute Python and Rust test suites.
---

# Goal

Treat lint plus tests as a single quality gate so that only code
that passes all checks gets committed.

# Project detection

- Use the same upward search for `Cargo.toml` and `pyproject.toml` as `/lint`
  to determine the Rust and Python project roots.
- Run `pytest` in the Python project root.
- Run `cargo test` in the Rust project root.

# Default behavior

When the user runs `/test`, perform the following steps:

1. Run the equivalent of the `/lint` command.
   - If there are fatal lint errors, report them and stop without running tests.
2. Python tests:
   - Run `pytest` in the Python project root.
3. Rust tests:
   - Run `cargo test` in the Rust project root.

If any test fails, analyze the logs and summarize:
- which step failed (lint / pytest / cargo test),
- the main error messages,
- suggested fixes.

# Options

- `/test python` :
  - Run `/lint python`, then only run `pytest`.
- `/test rust` :
  - Run `/lint rust`, then only run `cargo test`.
- `/test fast` :
  - Run full lint.
  - Run tests while skipping slow or long-running tests
    (e.g. using markers or separate test suites).
