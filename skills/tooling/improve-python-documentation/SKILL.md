---
name: improve-python-documentation
description: Improve docstrings and comments in an explicit Python file or directory scope after implementation is complete. Use when Python code needs clearer documentation without changing behavior.
---

# Improve Python Documentation

Improve docstrings and comments inside one explicit Python file or directory scope. If no scope is provided, stop and ask the caller to identify the files or directory.

## Review The Scope

Read the selected Python files and the relevant project instructions, component specification, configuration, tests, and nearby implementation when they are needed to understand the code. Keep every write inside the selected scope.

Use the project's existing documentation conventions when they are clear. Otherwise, use a simplified Google-style:

- Give public modules, classes, functions, and methods a concise summary when they need one.
- Add `Args`, `Returns`, `Yields`, `Raises`, or `Note` sections only when the corresponding semantics are non-obvious or important to the interface.
- Use comments for non-obvious rationale, constraints, shape/dtype/device assumptions, masks, or compatibility branches. Explain why the code matters rather than restating what it does.
- Keep simple code and obvious private helpers concise.

## Improve Safely

Directly improve existing docstrings and comments and add missing documentation when its meaning is clear from the code and the available project context. Preserve behavior, public interfaces, and the component contract.

When the meaning is uncertain, a comment would require an unsupported scientific or historical claim, or the Python implementation conflicts with a Markdown component contract, leave the code unchanged and report the file, location, and reason. Do not resolve design or contract questions inside this skill.

## Report

Report the explicit scope, changed files, documentation changes, skipped or uncertain items, and any contract inconsistencies. State clearly when no documentation changes were needed.
