---
name: pyo3-maturin-build-and-devloop
description: Build and development workflow for PyO3 + maturin projects.
---

# Goal

Unify the development-time and production-time build workflow
for PyO3 + maturin projects to avoid confusion.

# Development prerequisites

- The Python side must always run inside a virtual environment
  (venv, uv, poetry, etc.).
- The Rust toolchain is managed via rustup, using the stable channel by default.

# Development loop

1. Activate the virtual environment.
2. Run `maturin develop` to install the extension into the active environment.
3. Import the module from Python code and verify behavior.
4. After changes, run `maturin develop` (or `maturin develop --release`) again.

# Build policy

- Use `maturin develop` for normal debugging and local development.
- Use `maturin build --release` for performance testing and release candidates.
- In CI, run `maturin build` for a minimal matrix of Python versions and platforms.

# Notes

- Do not use `python setup.py` based build flows; maturin is the single build entry point.
- If the Python side cannot import the Rust module, first check:
  - whether `maturin develop` has been run, and
  - whether the correct virtual environment is active.
