# Execute Fixed Experiments Through One Entry

`run-experiment` is the only user-facing execution entry. It reads the environment configuration, phases, commands, resources, checkpoint flow, outputs, and failure boundaries already fixed by `define-experiment`; it does not accept temporary execution overrides or define a generic environment, output, Result, or report format.

The Experiment must declare its environment configuration. The target project's existing convention determines how that configuration is prepared. `run-experiment` executes the declared phases and checks the declared outputs; Experiment code produces any environment report or other output that the Experiment declares. The skill does not add an environment-probing command or record runtime facts by default.

Automatic Experiment commits are permitted only after the user explicitly confirms the exact diffs, paths, and commit message shown immediately before commit creation. A later or changed submission set requires new confirmation. Result commits remain user-created and contain only files the Experiment declares or the user chooses to retain.

## Consequences

Users have one execution entry without a generic execution schema. Environment requirements and outputs belong to the Experiment contract; project-native configuration expresses package and Python versions, and no generic CUDA or hardware requirement is imposed. Formal execution still requires a fixed Experiment control commit and the named Model, Dataset derivation, and Benchmark commits. Explicitly authorized exploratory runs may use incomplete provenance but cannot be presented as formal evidence.
