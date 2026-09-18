---
name: define-experiment
description: Define one concrete PyTorch Experiment spec from a fixed model and a confirmed Benchmark without executing it. Use for model, seed, training, phase, resource, checkpoint, and output choices; use define-benchmark for evaluation protocols.
---

# Define Experiment

Define one concrete Experiment at a time in `experiments/<experiment-id>/experiment.yaml`. Read the selected model, confirmed Benchmark, its Dataset setup references, dependency identity, and target-project phase interfaces. This skill consumes component definitions; it does not create or modify them.

## Preconditions

Require a Benchmark whose `status` is `confirmed` and whose complete directory has a user-created component commit. Verify its repository, complete 40-character commit SHA, and paths, and verify that its Dataset setup references match their confirmed component commits. If the Benchmark is missing, incomplete, or needs different inputs, metrics, aggregation, or visualizations, stop and hand the work to `$define-benchmark`.

Read the selected model as a committed component. If the model or a Dataset setup must change, hand the work to `$implement-model` or `$dataset-setup`. Modify only `experiments/<experiment-id>/experiment.yaml` from this skill.

## Git revision contract

For formal work, replace every placeholder with a repository, a complete 40-character commit SHA, and the paths that define the component. An external repository is allowed when its URL or identifier and use are recorded and its commit is independently verifiable. Do not use a branch, tag, short SHA, `latest`, dirty-worktree marker, or `locator@revision` in place of a commit. Components in one repository may use different commits; the formal runner verifies that each source checkout matches each declared path at its commit.

## Experiment spec

Use for one concrete model/configuration/seed and one or both independent phases:

```yaml
id: experiment-<stable-id>
status: draft | confirmed
mode: train-evaluate | evaluation-only
benchmark: benchmark-<id>
model: <model-id>
component_commits:
  model:
    repository: <Git remote or repository identifier>
    commit: <40-character SHA>
    paths: [<model paths>]
  dataset_setups:
    - id: dataset-setup-<id>
      repository: <Git remote or repository identifier>
      commit: <40-character SHA>
      paths: [<data/<dataset-id>/setups/<setup-id> paths>]
    - id: dataset-setup-<another-id>
      repository: <Git remote or repository identifier>
      commit: <40-character SHA>
      paths: [<data/<dataset-id>/setups/<another-id> paths>]
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

For `evaluation-only`, omit `train`, set `training.status` to `not_applicable`, include an External checkpoint record and a non-empty compatibility declaration covering architecture/model revision, format, preprocessing, shape/dtype, labels, and source conditions. The model, every Dataset setup selected by the Benchmark, the Benchmark including its metric implementations, dependencies, and Experiment control commit still require Git commits.

Do not start execution from this skill. Show the fixed component revisions, Benchmark input mappings, optional training settings, phases, resource plan, checkpoint, outputs, and target file before writing. Keep `status: draft` until the user explicitly confirms the complete Experiment spec. After that confirmation, write `status: confirmed` only in `experiments/<experiment-id>/experiment.yaml`, then show `git diff`, the exact file to commit, and a proposed commit message. Do not create the commit. The Experiment is eligible for formal execution only after the user commits the confirmed spec and records that complete SHA as the Experiment control commit. Hand it to `$run-experiment`, `$train-experiment`, or `$evaluate-experiment` only when the user requests execution.
