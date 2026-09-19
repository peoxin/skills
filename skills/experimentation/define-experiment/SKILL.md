---
name: define-experiment
description: Define one concrete PyTorch Experiment with selected component paths, phases, configuration or custom phase code, and outputs, without executing it.
---

# Define Experiment

Treat each Experiment as one concrete training, evaluation, or train-then-evaluate declaration. It selects reusable components and fixes the phase behavior, configuration, resources, checkpoint flow, and outputs for one run design. It does not define Model, Dataset, or Benchmark components and does not execute the Experiment.

## Workflow

1. Inspect the target project's instructions, selected component paths, training and evaluation entry points, configuration and implementation conventions, and dependency conventions.
2. Before defining or changing an Experiment, invoke the `grill-with-docs` skill. Resolve the question and comparison, component paths, environment configuration, declared phases, configuration, resources, checkpoint flow, outputs, and failure boundaries. Continue only after the design is explicit and confirmed. If it is unavailable, stop.
3. Create or update `EXPERIMENT.md` inside `experiments/<experiment-id>/`.
4. Implement the declared phases using the target project's existing configuration or code conventions. Create or update project-native configuration, custom phase code, or both as required.
5. Keep `EXPERIMENT.md`, configuration or phase code, and commands consistent. If the question, components, phases, configuration, implementation, resources, checkpoint flow, outputs, or failure boundaries change, pause and repeat the `grill-with-docs` skill before continuing.
6. List the changed files, show the diff, and propose an Experiment control commit message. Do not create a commit or execute the Experiment.

If a selected Model, Dataset derivation, or Benchmark must change, hand the work to the `define-model`, `define-dataset`, or `define-benchmark` skill.

## Experiment Directory

Use one directory for each concrete Experiment:

```text
experiments/
  <experiment-id>/
    EXPERIMENT.md
    configs/
      <configuration files>
    <implementation files>
```

The optional `configs/` directory contains configuration consumed directly by the target project or framework. The Experiment may also contain custom phase implementation files when the target project does not provide the required training or evaluation entry point. Use existing project conventions; do not require a framework, schema, filename, or one configuration file per phase. Keep Experiment-specific training and evaluation configuration and code inside the Experiment directory; keep architecture and model-behavior configuration in the Model component.

## EXPERIMENT.md

`EXPERIMENT.md` is the human-readable contract for the Experiment. Keep it concise and consistent with its configuration and commands.

Use these sections:

### Description

Describe the research question, comparison, purpose, and explicit boundaries of the Experiment.

### Components

Identify the Model, Dataset derivations, Benchmark, and other inputs used by the declared phases. Record their logical identities, roles, repositories when external, and paths. Record component versions in the Experiment control commit message, not in this document.

A Train phase requires a Model and its training Dataset derivations. A Benchmark is optional unless training consumes its protocol. An Evaluate phase requires a Model, Benchmark, and checkpoint; the Benchmark identifies its Dataset derivations and evaluation inputs.

### Environment

Identify the environment configuration files or directory used by the Experiment, such as `pyproject.toml`, `requirements.txt`, `uv.lock`, `poetry.lock`, or `environment.yml`. Record the paths and use the target project's existing environment convention. Do not duplicate configuration content in `EXPERIMENT.md` or require a setup command here. The configuration files express the Python and package versions used by the Experiment; this section does not define CUDA or hardware requirements.

### Phases

Use `#### Train` and `#### Evaluate` subsections for the phases that apply. Declare at least one phase.

For Train, record the data inputs, command or entry point, configuration paths, seed, resources, checkpoint selection, and continuation or failure conditions. Validation used for early stopping, checkpoint selection, or training monitoring remains part of Train.

For Evaluate, record the Benchmark inputs, command or entry point, configuration paths when needed, resources, checkpoint source, and failure conditions. An evaluation-only Experiment may consume an external checkpoint or a checkpoint from a prior Result. Record its locator, digest when available, provenance, and compatibility requirements. In a train-then-evaluate Experiment, reference the selected checkpoint produced by Train; the Result records its final path and digest.

Evaluation configuration controls execution parameters such as batch size, workers, precision, devices, checkpoint selection, and output locations. It may select a declared Benchmark input or an explicitly allowed metric subset, but it must not redefine the Benchmark's metrics, masking, reduction, aggregation, or visualizations. Omit evaluation configuration when the target project does not need it.

### Outputs

Describe the checkpoints, training metrics, Benchmark metrics, visualizations, reports, logs, and other outputs produced by the declared phases, including their expected locations or location rules.

### Implementation

Record the phase commands, configuration files, custom phase code, environment files, and implementation choices needed to run the Experiment. Keep optimizer, scheduler, learning rate, batch size, epochs, seed, trainer, precision, resources, and other run-specific settings in the Experiment configuration or implementation when the target project consumes them there.

The specification must remain consistent with the configuration and commands. If implementation changes the Experiment contract, stop and realign the design before continuing.

## Versioning

An Experiment is a concrete definition, not a reusable component. Before formal execution, every selected Model, Dataset derivation, and Benchmark must be fixed by an existing component commit or by a new commit containing only that component. Components that already have the required fixed revision do not need another commit.

The Experiment control commit fixes `EXPERIMENT.md` and its configuration. Its commit message records every selected component's repository, complete commit SHA, and paths using this body format:

```text
Components:
- model: repository=<repository>; commit=<40-character SHA>; paths=<paths>
- dataset-derivation: repository=<repository>; commit=<40-character SHA>; paths=<paths>
- benchmark: repository=<repository>; commit=<40-character SHA>; paths=<paths>
```

Repeat entries when multiple Dataset derivations apply. Formal execution rejects missing entries, branches, tags, short SHAs, dirty selected paths, or paths that do not match the recorded commit. Environment configuration paths are fixed by the Experiment control commit when they are in the same repository and are not repeated in the commit message.

The user may create these commits manually. A pre-run workflow may create them automatically only when the user selects that option: show each component diff, commit each changed reusable component separately, then commit the Experiment definition with the component bindings in its message. Do not include unrelated or parallel work.
