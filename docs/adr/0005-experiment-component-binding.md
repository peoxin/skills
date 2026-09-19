# Bind Experiment Components Through Git

An Experiment is a concrete definition rather than a reusable component. Its `EXPERIMENT.md` records component paths and its configuration or custom phase code records how the declared phases run; before formal execution, the Experiment control commit message binds each selected Model, Dataset derivation, Benchmark, and dependency to its repository, complete commit SHA, and paths. This avoids a duplicate lock file while preserving independently versioned reusable components and allowing target projects to use their own framework or implementation style.

## Consequences

Reusable components are committed independently when they need a new revision; an existing fixed revision may be reused. The Experiment control commit fixes only the Experiment definition and its configuration or custom phase code, and a pre-run workflow may create the component commits separately when the user permits it. Runners must parse and verify the control commit message, and an Experiment directory no longer requires a universal `experiment.yaml` record.
