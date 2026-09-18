---
name: define-experiment
description: Define reusable benchmark contracts and concrete PyTorch experiment specs without starting formal execution.
---

# Define Experiment

Read the confirmed Dataset setups and the target project's model, Benchmark, dependency, and phase interfaces. Keep the reusable data-and-metric protocol separate from one executable instance.

Default locations are `benchmarks/<benchmark-id>/benchmark.yaml` and `experiments/<experiment-id>/experiment.yaml`. Logical directory names are stable component identities, not version labels.

## Git revision contract

For formal work, replace every placeholder with a repository, a complete 40-character commit SHA, and the paths that define the component. An external repository is allowed when its URL or identifier and use are recorded and its commit is independently verifiable. Do not use a branch, tag, short SHA, `latest`, dirty-worktree marker, or `locator@revision` in place of a commit. Components in one repository may use different commits; the formal runner verifies that each source checkout matches each declared path at its commit.

## Benchmark spec

A Benchmark defines how one or more Dataset setups become named evaluation inputs and which metrics interpret those inputs. It does not define baselines, models, seeds, resource requests, or a concrete checkpoint. Keep the Benchmark self-contained, including its metric implementations and optional visualization code:

```text
benchmarks/
  <benchmark-id>/
    benchmark.yaml
    metrics/
    visualizations/
```

Use this template:

```yaml
id: benchmark-<stable-id>
task: <task definition>
dataset_setups:
  - id: dataset-setup-<id>
    role: train | validation | test | auxiliary
  - id: dataset-setup-<another-id>
    role: test | auxiliary
inputs:
  train: [<dataset setup input references>]
  validation: [<dataset setup input references>]
  test: [<dataset setup input references>]
  custom-input: [<dataset setup input references>]
composition:
  train: {operation: concat | interleave | join, rule: <composition rule>}
  test: {operation: <operation>, rule: <composition rule>}
metric_inputs:
  <metric-id>: [<named benchmark inputs>]
metrics:
  - id: <metric-id>
    direction: higher | lower
    units: <unit>
    implementation_paths: [benchmarks/<benchmark-id>/metrics/<paths>]
aggregation:
  statistic: mean_and_std
  confidence_or_uncertainty: <rule>
qualitative_outputs: [<optional visualization requirements>]
unavailable_inputs: [<input name and reason>]
```

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

For `evaluation-only`, omit `train`, set `training.status` to `not_applicable`, include an External checkpoint record and a non-empty compatibility declaration covering architecture/model revision, format, preprocessing, shape/dtype, labels, and source conditions. The model, every Dataset setup selected by the Benchmark, the Benchmark (including its metric implementations), dependencies, and Experiment control commit still require Git commits.

Do not start execution from this skill. Show component revisions, Benchmark input mappings, optional training settings, phase inputs, resource plan, and confirmation status. Once the Experiment spec is confirmed, commit `experiments/<experiment-id>/experiment.yaml` and record its commit as the Experiment control commit before handing it to `$run-experiment`, `$train-experiment`, or `$evaluate-experiment` for a direct phase request.
