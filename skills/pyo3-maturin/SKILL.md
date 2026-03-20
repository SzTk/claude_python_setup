---
name: pyo3-maturin
description: Standard guidelines and workflows for developing Rust extensions for Python using PyO3 + maturin.
---

This skill provides guidelines for developing Rust extension modules
for Python using **PyO3 + maturin**.

## 1. Project structure

- Rust crates must set `crate-type = ["cdylib"]` in `Cargo.toml`.
- The project assumes you used `maturin init` or `maturin new -b pyo3` as a baseline.
- Keep the mapping between the Python package name and the Rust crate name explicit and consistent.

See `./rules/project-structure.md` for details.

## 2. Build and development loop

- During development, prefer `maturin develop` to install the extension into the active virtual environment.
- For performance testing and release builds, use `maturin develop --release` or `maturin build --release`.
- If possible, consider using maturin’s import hook so the module is built automatically on import during development.

See `./rules/build-and-develop.md` for details.

## 3. Python–Rust boundary design

- Functions that are called directly from Python must use `#[pyfunction]`,
  and types that are instantiated from Python must use `#[pyclass]`.
- Map `Result<T, E>` to Python exceptions and `Option<T>` to `None` on the Python side.
- Define error types with `thiserror` (or similar) and map them 1:1 to custom Python exception classes.

See `./rules/api-design.md` for details.

## 4. Types, ownership, performance

- For large buffers, use slices like `&[u8]` or `&[f32]` in Rust, and pass them from Python as `bytes`, `memoryview`, or NumPy arrays.
- Avoid unnecessary copies, especially when converting between `String` and `str` or between Python and Rust container types.
- Avoid `unsafe` unless it is strictly necessary. When you use it, document the assumptions and invariants clearly.

## 5. Testing strategy

- Test core logic in Rust using unit and integration tests.
- Test Python usage paths with `pytest` after running `maturin develop`.
- For boundary behavior (`None`, exceptions, edge values), prioritize tests on the Python side.

## 6. Documentation and distribution

- All public Python APIs must have docstrings and type hints.
- Build wheels with `maturin build` and distribute them from `target/wheels`,
  verifying changelog and compatibility before publishing.
- Keep distribution metadata in `pyproject.toml` and `Cargo.toml` (license, classifiers, keywords, etc.) up to date.
