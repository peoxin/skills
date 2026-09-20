---
name: fix-python-quality
description: Fix Ruff formatting and lint errors in an explicit Python file or directory scope. Use when a user asks to format, lint-fix, or repair Ruff quality failures in a uv project.
---

# Fix Python Quality

Repair Ruff formatting and lint failures inside one selected scope. Assume the project owns its configuration; do not configure tools, run tests, or change behavior to satisfy a style rule.

## Select The Scope

Require the caller to provide an explicit Python file or directory scope. If no scope is provided, stop and ask the user or calling skill to identify the files or directory.

## Require Ruff

Run `uv run ruff --version`. If the command fails, stop and report that Ruff is unavailable. Accept uv creating or synchronizing the environment and updating the lockfile during this command.

## Repair And Verify

Run these commands in order with the exact selected paths:

```text
uv run ruff check --fix <scope>
uv run ruff format <scope>
uv run ruff check <scope>
uv run ruff format --check <scope>
```

Continue through all four commands when Ruff reports lint or formatting failures. Stop immediately when a command cannot start, the scope is invalid, or Ruff cannot parse the project configuration.

If lint failures remain, repair the Ruff-reported problems manually inside the selected scope while preserving behavior and public interfaces, then rerun the complete command sequence. Stop and report the unresolved failures when a repair would change behavior or a public interface, touch files outside the scope, or require an uncertain suppression.

Report the selected scope, changed files, every command result, remaining failures, and any uv-created environment or lockfile changes.
