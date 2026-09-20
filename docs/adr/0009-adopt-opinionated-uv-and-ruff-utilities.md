# Adopt Opinionated uv And Ruff Utilities

The Utilities module provides two independent, preference-specific Python capabilities. `initialize-python-project` is user-invoked and initializes only an absent or empty target with the installed uv version's defaults, a fixed Ruff development dependency and configuration, and a fixed `.gitignore`. `fix-python-quality` is model-invocable and runs Ruff through uv to repair and verify formatting and linting in an explicit Python file or directory scope. `improve-python-documentation` is model-invocable and improves docstrings and comments in an explicit Python file or directory scope.

These utilities optimize for one user's workflow rather than compatibility. The initializer does not migrate existing projects or establish a contract consumed by other skills. The repair skill requires `uv run ruff --version` to succeed but does not inspect the resolved executable path, configure tooling, provide a check-only mode, or run tests. Python-producing skills continue to run the target project's established quality commands directly and do not invoke either utility.

## Consequences

The initializer follows future changes to ordinary `uv init` output except for the complete `.gitignore` and Ruff configuration that it owns. It accepts uv environment synchronization and lockfile updates during `uv run`. Partial initialization remains on disk for diagnosis instead of being deleted automatically.

The old `python-quality` identity, capability profiles, and configure/check/fix modes are removed without a compatibility alias. This decision supersedes ADR-0007.
