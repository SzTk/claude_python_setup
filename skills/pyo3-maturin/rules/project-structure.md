---
name: pyo3-maturin-project-structure
description: Directory layout and responsibility split for PyO3 + maturin projects.
---

# Goal

Define the directory structure and responsibility split when developing
a Python application and a Rust extension in the same repository.

# Structure principles

- Keep the Python application code and the Rust extension code at separate top-level paths.
- The Python side uses a standard layout with `pyproject.toml` and a package directory.
- The Rust side is a library crate with `Cargo.toml` and `src/`, producing a `cdylib`.

# Recommended layout

- backend/               # example project root
  - pyproject.toml       # Python side (e.g. FastAPI)
  - app/                 # Python package
    - __init__.py
    - api/
    - services/
    - adapters/
      - rust_image_proc.py  # Python wrapper around the Rust extension
  - rust/                # Rust side (PyO3)
    - Cargo.toml
    - src/
      - lib.rs

# Cargo.toml assumptions

- The `[lib]` section must include `crate-type = ["cdylib"]`.
- The crate name should generally match the module name imported from Python.
- The `pyo3` dependency must enable the `extension-module` feature.

# Responsibility split

- Python:
  - Handles I/O concerns such as web APIs, CLIs, and configuration.
  - Orchestrates business logic and workflows.
- Rust:
  - Handles compute-intensive logic and low-level data operations.
  - Exposes functions with minimal side effects when possible.
