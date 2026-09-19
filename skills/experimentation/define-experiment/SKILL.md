---
name: define-experiment
description: Define one concrete PyTorch Experiment from fixed Model and Benchmark revisions without executing it. Use for model, seed, training, phase, resource, checkpoint, and output choices; use define-benchmark for evaluation protocols.
---

# Define Experiment

Define one concrete Experiment at a time in `experiments/<experiment-id>/EXPERIMENT.md` and `experiments/<experiment-id>/experiment.yaml`. Read the selected Model and Benchmark revisions, the Benchmark's Dataset derivation references, dependency identity, and target-project phase interfaces. This skill consumes component definitions; it does not create or modify them.

## Preconditions

Require a Benchmark whose complete directory, including consistent `BENCHMARK.md`, `benchmark.yaml`, metric implementations, and visualizations, has a user-created component commit. Verify its repository, complete 40-character commit SHA, and paths, and verify that its Dataset derivation references match their committed component revisions. If the Benchmark is missing, inconsistent, or needs different inputs, metrics, aggregation, or visualizations, stop and hand the work to `$define-benchmark`.

Read the selected model as a committed component. If the model or a Dataset derivation must change, hand the work to `$implement-model` or `$define-dataset`. Modify only `experiments/<experiment-id>/EXPERIMENT.md` and `experiments/<experiment-id>/experiment.yaml` from this skill.

## Align the Experiment specification

Before creating or normatively changing an Experiment, invoke `$grill-with-docs`. If it is unavailable, stop. Work the design tree until its frontier is empty, present the complete shared understanding, and wait for explicit user confirmation before writing either Experiment file.

Keep `EXPERIMENT.md` brief and use these sections:

- **Research question and comparison**: the question, comparison, and explicit scope.
- **Fixed components**: the Model, Dataset derivations, Benchmark, dependencies, and their roles.
- **Phases and resources**: train/evaluate phases, commands, checkpoint flow, seed, and resource envelope.
- **Outputs**: expected checkpoints, metrics, reports, and their locations.
- **Success and failure criteria**: interpretation boundaries, acceptance evidence, and conditions that stop or invalidate the run.

`EXPERIMENT.md` is the human-readable contract. `experiment.yaml` holds exact machine-consumed fields and must remain consistent with it without copying the full YAML into Markdown.

## Git revision contract

For formal work, replace every placeholder with a repository, a complete 40-character commit SHA, and the paths that define the component. An external repository is allowed when its URL or identifier and use are recorded and its commit is independently verifiable. Do not use a branch, tag, short SHA, `latest`, dirty-worktree marker, or `locator@revision` in place of a commit. Components in one repository may use different commits; the formal runner verifies that each source checkout matches each declared path at its commit.

## Experiment spec

Use for one concrete model/configuration/seed and one or both independent phases:

```yaml
id: experiment-<stable-id>
mode: train-evaluate | evaluation-only
benchmark: benchmark-<id>
model: <model-id>
component_commits:
  model:
    repository: <Git remote or repository identifier>
    commit: <40-character SHA>
    paths: [<model paths>]
  dataset_derivations:
    - id: dataset-derivation-<id>
      repository: <Git remote or repository identifier>
      commit: <40-character SHA>
      paths: [<data/<dataset-id>/derivations/<derivation-id> paths>]
    - id: dataset-derivation-<another-id>
      repository: <Git remote or repository identifier>
      commit: <40-character SHA>
      paths: [<data/<dataset-id>/derivations/<another-id> paths>]
  benchmark:
    repository: <Git remote or repository identifier>
    commit: <40-character SHA>
    paths: [<benchmark paths>]
  dependencies:
    repository: <Git remote or repository identifier>
    commit: <40-character SHA>
    paths: [<lockfile paths>]
seed: <integer>
resource_request:
  gpu_count: <positive integer>
  allowed_indices: [<GPU index>]
  min_free_memory_mb: <integer>
  queue_timeout_seconds: <number>
outputs: {checkpoint: <path or locator>, metrics: <path or locator>}
training:
  status: applicable | not_applicable
  reason: <required when not_applicable>
  optimizer: <optimizer>
  scheduler: <scheduler>
  batch_size: <integer>
  epochs: <integer>
phases:
  - name: train
    command: <target-project command>
    inputs: [<Benchmark input names>]
  - name: evaluate
    command: <target-project command>
    checkpoint: <path or external checkpoint reference>
    benchmark_inputs: [test | validation | <custom input>]
    allow_partial_train: false
```

For `evaluation-only`, omit `train`, set `training.status` to `not_applicable`, include an External checkpoint record and a non-empty compatibility declaration covering architecture/model revision, format, preprocessing, shape/dtype, labels, and source conditions. The model, every Dataset derivation selected by the Benchmark, the Benchmark including its metric implementations, dependencies, and Experiment control commit still require Git commits.

Do not start execution from this skill. After alignment, write both Experiment files. If resolving exact fields requires a change to the research question, comparison, components, phases, resources, outputs, success criteria, or failure conditions, pause and repeat `$grill-with-docs`; formatting and mechanical changes that preserve the specification do not require another interview.

Verify that `EXPERIMENT.md` and `experiment.yaml` agree. Show `git diff`, the exact files to commit, and a proposed commit message. Do not create the commit. The Experiment is eligible for formal execution only after the user commits the complete Experiment directory and records that full SHA as the Experiment control commit. Hand that fixed revision to `$run-experiment`, `$train-experiment`, or `$evaluate-experiment` only when the user requests execution.
