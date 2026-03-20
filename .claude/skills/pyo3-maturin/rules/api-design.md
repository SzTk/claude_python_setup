---
name: pyo3-maturin-api-design
description: Design guidelines for PyO3 APIs consumed from Python.
---

# Goal

Standardize how functions and types are exposed through PyO3 so that
they are convenient to use from Python while preserving Rust’s safety.

# What to expose

- Use `#[pyfunction]` for functions that should be called directly from Python.
- Use `#[pyclass]` for types that should be instantiated or held on the Python side.
- Do not annotate internal-only functions or types with PyO3 macros.

# Function signature guidelines

- Group work into functions that can complete in a single call.
- Prefer Python-friendly argument shapes:
  - `bytes` / `bytearray`
  - `str`
  - `int` / `float`
  - `dict` / `list`
- In Rust, represent large data (images, buffers) as slices or structs, and convert
  from `bytes`, `memoryview`, or NumPy arrays on the Python side.

# Errors and Result

- Use `PyResult<T>` for PyO3-exposed functions.
- Internally, use `Result<T, E>` where `E` is an error type defined with `thiserror` (or similar).
- Map all errors to concrete Python exception classes.
  Example: `ImageError::DecodeFailed` → `ImageDecodeError` in Python.

# API layering

- Rust:
  - Pure Rust API (business logic).
  - Thin PyO3 wrapper layer around the pure Rust API.
- Python:
  - An adapters layer that imports the PyO3 functions and normalizes signatures
    to Python-friendly types.
  - A services layer that only talks to Rust through the adapters.

# Compatibility

- Treat changes to public function names or arguments as breaking changes.
  Update Python wrappers and tests alongside them.
- When backward compatibility is required, keep the old API as deprecated for a transition period.
