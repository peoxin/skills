# Bind Experiment Components Through Git

An Experiment is a concrete definition rather than a reusable component. Its `EXPERIMENT.md` records component paths, environment configuration paths, and its phase behavior; its configuration or custom phase code records how the declared phases run. Before formal execution, the Experiment control commit fixes the Experiment directory and any same-repository environment files it references. The commit message binds each selected Model, Dataset derivation, and Benchmark to its repository, complete commit SHA, and paths.

Environment configuration is not repeated in the control commit message. An external environment is recorded in `EXPERIMENT.md` by immutable locator or version and verified independently. The target project's environment convention determines how the configuration is prepared; the generic execution skill does not define a universal installer or environment record.

## Consequences

Reusable Model, Dataset derivation, and Benchmark components remain independently committed. The Experiment control commit fixes the concrete Experiment and its same-repository environment inputs without requiring a universal experiment lock file or dependency binding block. `run-experiment` verifies the minimum commit preconditions, prepares the declared environment using project convention, executes the declared phases, and checks the declared outputs. Result files are determined by the Experiment rather than by a generic Result schema.
