---
name: run-experiment
description: Run one fixed Experiment using its declared environment, phases, resources, and outputs.
---

# Run Experiment

`run-experiment` executes an Experiment that `define-experiment` has already specified. It does not design the Experiment, infer missing environment behavior, define output formats, or add execution records that the Experiment did not request.

## Workflow

1. Read the target project's instructions, the fixed Experiment directory, `EXPERIMENT.md`, its environment configuration, phase implementation, component paths, and dependency conventions.
2. Confirm that the Experiment control commit exists and that the Experiment path is clean. Confirm that the Model, Dataset derivation, and Benchmark commits named by the control commit message exist.
3. If the Experiment has not been committed, or an already prepared environment file must be included in the Experiment control commit, show the exact diff, paths, and commit message. Create that commit only after the user explicitly confirms that exact submission set.
4. Prepare the declared environment using the target project's existing convention and the environment configuration named by the Experiment. Do not choose an environment tool or invent a setup step when the project convention is unclear; stop and ask for the missing project instruction.
5. Allocate the resources declared by the Experiment. Do not silently change the request or add a generic scheduler.
6. Execute only the phases declared by the Experiment, in their declared order, using their commands, configuration, inputs, and resource assignments. Do not add a phase or explicitly invoke a separate environment-report command; Experiment code produces any declared outputs.
7. Check that every output declared by the Experiment exists at its declared location and report the phase and output results. Do not impose a default output directory, manifest format, environment report, or Result schema.
8. Show the files and proposed message for a user-created Result commit. Do not create the Result commit automatically.

If a phase, environment configuration, resource, command, failure rule, or output needs to change, stop and hand the change to `$define-experiment`. A failed phase is reported as failed; a missing declared output blocks completion. Exploratory execution is allowed only when the user explicitly requests it and must be described as exploratory.

## Commit Boundary

`run-experiment` may commit an already prepared Experiment directory and the environment files it references when they belong to the same repository. It may not write or revise `EXPERIMENT.md`, add an environment file, change a phase, change a command, or add an output. Those are `define-experiment` changes.

Before creating an Experiment control commit, show the exact files, diff, and message and obtain explicit user confirmation. A later or different file set requires a new confirmation. The control commit fixes the Experiment directory and the paths it references in that repository; environment files are not repeated in its `Components:` commit-message entries. An external environment is referenced by an immutable locator or version in `EXPERIMENT.md`.

The user creates the Result commit after reviewing the produced files. `run-experiment` proposes the files and message but never creates that commit implicitly.
