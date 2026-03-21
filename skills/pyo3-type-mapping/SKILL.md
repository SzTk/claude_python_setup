---
name: pyo3-type-mapping
description: Guide for correct Python↔Rust type conversions in PyO3. Use when writing or reviewing Rust functions exposed to Python via PyO3 — especially when dealing with binary data (bytes), collections, or custom pyclass structs.
---

## The One Rule

**Never assume a Rust type and its "obvious" Python equivalent are the same.**
Always verify the actual PyO3 conversion. The most dangerous mismatch is binary data.

## Binary Data — The Common Trap

### ❌ Wrong — `Vec<u8>` returns `list[int]` in Python, NOT `bytes`

```rust
#[pyfunction]
fn encode_image(img: PyRef<ImageHandle>) -> PyResult<Vec<u8>> {
    Ok(buf)  // Python receives [137, 80, 78, 71, ...] — a list of ints!
}
```

This causes cryptic downstream errors like:
```
AttributeError: 'list' object has no attribute 'encode'
```

### ✅ Correct — use `Py<PyBytes>` to return Python `bytes`

```rust
use pyo3::types::PyBytes;

#[pyfunction]
fn encode_image(py: Python<'_>, img: PyRef<ImageHandle>) -> PyResult<Py<PyBytes>> {
    let buf: Vec<u8> = /* ... */;
    Ok(PyBytes::new_bound(py, &buf).unbind())  // PyO3 0.21–0.22
    // PyO3 ≥ 0.23: PyBytes::new(py, &buf).unbind()
}
```

`py: Python<'_>` as the first parameter is injected automatically by PyO3 — invisible to Python callers.

## Type Mapping Reference

| Rust return type | Python type | Correct? |
|---|---|---|
| `Vec<u8>` | `list[int]` | ⚠️ Only if you actually want a list |
| `Py<PyBytes>` / `Bound<'_, PyBytes>` | `bytes` | ✅ Use for binary output |
| `&[u8]` (parameter) | `bytes` / `bytearray` | ✅ Accepts Python `bytes` as input |
| `Vec<T>` (T ≠ u8) | `list[T]` | ✅ |
| `String` / `&str` | `str` | ✅ |
| `i32` / `u32` / `i64` / `u64` | `int` | ✅ |
| `f32` / `f64` | `float` | ✅ |
| `bool` | `bool` | ✅ |
| `Option<T>` | `T \| None` | ✅ |
| `HashMap<K,V>` | `dict` | ✅ |
| `#[pyclass]` struct | Python object | ✅ Pass via `PyRef<T>` / `PyRefMut<T>` |

## Checklist Before Adding a New PyO3 Function

- [ ] Returns binary data? → Use `Py<PyBytes>` + `PyBytes::new_bound(py, &buf).unbind()`
- [ ] Takes binary data as input? → `&[u8]` is fine, no special handling needed
- [ ] Takes/returns a `#[pyclass]`? → `PyRef<T>` (immutable) or `PyRefMut<T>` (mutable) for parameters; return by value for output
- [ ] After writing: run `python -c "import mod; print(type(mod.my_fn(...)))"` to confirm the actual Python type before writing tests
