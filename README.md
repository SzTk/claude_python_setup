# claude_python_setup

A collection of instruction and convention templates for **Claude Code** to use when developing Python + Rust extension libraries.
Simply place this repository in your project — the Claude Code agent will pick up the skills, commands, and agent definitions as needed.

> Japanese version: [README.ja.md](README.ja.md)

## Structure

```
skills/
  pyo3-maturin/        # Conventions for Rust extension development with PyO3 + maturin
  pyo3-type-mapping/   # Guidelines for Python–Rust type mapping
  python-uv-workflow/  # Standard workflow for Python virtual environment management with uv
commands/
  python-env.md        # /python-env  : Set up and verify the virtual environment
  lint.md              # /lint        : Static analysis for Python (ruff/mypy) + Rust (clippy)
  test.md              # /test        : Run lint + pytest + cargo test in one step
agents/
  pre-commit-test.md   # Agent that runs /test before each git commit as a quality gate
```

## Prerequisites

| Tool | Purpose |
|------|---------|
| [uv](https://github.com/astral-sh/uv) | Python virtual environment and package management |
| [maturin](https://github.com/PyO3/maturin) | Build Rust → Python extensions |
| [ruff](https://github.com/astral-sh/ruff) | Python lint / formatter |
| [mypy](https://mypy-lang.org/) | Python type checking |
| Rust toolchain (`cargo`, `clippy`) | Rust build and lint |

## Usage

1. Place the contents of this repository in your target project.
2. The Claude Code agent will autonomously reference each file as work requires.

You can also explicitly invoke commands such as `/python-env`, `/lint`, or `/test` when needed.

## Skills overview

### `python-uv-workflow`

Instructs Claude to run all Python work through `uv run ...`.
Prohibits direct calls to global `python` / `pip` / `pytest` and handles automatic `.venv` creation and sync.

### `pyo3-maturin`

Defines the development workflow for Rust extensions using PyO3 + maturin.
Covers project structure, build loop, Python–Rust boundary design, type mapping, and error conversion rules.

### `pyo3-type-mapping`

A reference guide for type correspondence between Python and Rust, including conversion caveats.

