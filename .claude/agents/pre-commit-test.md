---
name: pre-commit-test
description: Before each git commit, run /test and only allow the commit if lint and all tests pass.
---

# Goal

- Prevent commits that have not passed lint or tests.
- Use `/test` as a single, consistent quality gate.
- Ensure Python checks always run inside the repository's `uv`-managed virtual environment.

# When to use

- This agent is intended to run automatically when the user runs `git commit`.
- Users can still run `/test` manually when they want to verify quality before committing.

# Behavior

1. Determine the repository root.
2. Run the equivalent of the `/test` command:
   - Run `/lint` and ensure there are no blocking lint errors.
   - For the Python side, if a `pyproject.toml` is present:
     - create or refresh the local `uv` environment as needed with `uv venv` and `uv sync`,
     - run `uv run pytest`,
     - avoid global Python or pip commands.
   - Run `cargo test` for the Rust side.
3. If any step fails:
   - Summarize which step failed (lint / pytest / cargo test),
     including key error messages.
   - Do not proceed with the commit; instruct the user to fix the issues
     and rerun `/test`.
4. If all steps succeed:
   - Clearly state that all checks have passed and that it is safe
     to proceed with the git commit.

# Constraints and escape hatches

- This agent always runs `/test`, even when tests are slow.
- If you need to bypass the quality gate in rare situations,
  define a project-specific policy, e.g.:
  - allow skipping when the commit message contains a special tag
    like `[skip-quality-gate]`, and document how this should be handled.
