---
name: initialize-python-project
description: Initialize a new uv-managed Python project with the preferred Ruff and Git ignore defaults.
disable-model-invocation: true
---

# Initialize Python Project

Create one new Python project using uv's current defaults, then install and configure Ruff and replace `.gitignore` with the fixed personal baseline.

## Collect Inputs

Require a target path and a Python version request. Pass the Python value to uv exactly as supplied; let uv validate and resolve it.

## Check Preconditions

1. Require `uv` on `PATH`; if it is unavailable, stop before writing anything.
2. Accept a target path that does not exist or an existing directory with no entries. Hidden files and directories count as entries. If the target contains anything, list the conflicting entries and stop.

## Initialize

The explicit invocation and required inputs authorize the initialization. Run without another confirmation:

```text
uv init --python <python> <target>
```

Use the installed uv version and its ordinary defaults. Do not select an app, library, script, package, build backend, VCS, or other template option.

After initialization, inspect the generated `pyproject.toml`. If it already contains any Ruff configuration, stop and preserve it rather than merging or replacing it. Otherwise, from the project root run:

```text
uv add --dev ruff
```

Append this exact configuration to `pyproject.toml` without reconstructing uv-generated fields:

```toml
[tool.ruff]
line-length = 120

[tool.ruff.lint]
select = [
  "E",    # pycodestyle errors
  "W",    # pycodestyle warnings
  "F",    # pyflakes
  "I",    # isort
  "B",    # flake8-bugbear
  "C4",   # flake8-comprehensions
  "UP",   # pyupgrade
  "SIM",  # flake8-simplify
]
ignore = ["E501"]  # line length handled by formatter

[tool.ruff.format]
quote-style = "double"
indent-style = "space"
```

Replace `.gitignore` with this exact file:

```gitignore
# Python-generated files
__pycache__/
*.py[oc]
build/
dist/
wheels/
*.egg-info

# Virtual environments
.venv

# Tool caches
.pyright/
.ruff_cache/
.mypy_cache/
.pytest_cache/

# Editors and operating systems
.vscode/
.idea/
.DS_Store
```

## Verify

Run:

```text
uv run ruff format --check .
uv run ruff check .
```

If either check fails, repair files owned by this new project with Ruff's formatter and safe fixes, then rerun both checks. Initialization is complete only when both pass.

If any step fails, preserve the project directory and report the target path, completed steps, failing command or conflict, and remaining work. Never delete or roll back the partially initialized directory automatically.
