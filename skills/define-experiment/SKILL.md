---
name: define-experiment
description: Define reusable benchmark contracts and concrete PyTorch experiment specs without starting formal execution.
---

# Define Experiment

Read the confirmed Dataset setup and the target project's model, training, evaluation, metric, and dependency interfaces. Keep a reusable comparison protocol separate from one executable instance.

## Git revision contract

For formal work, replace every placeholder with a repository, a complete 40-character commit SHA, and the paths that define the component. An external repository is allowed when its URL or identifier and use are recorded and its commit is independently verifiable. Do not use a branch, tag, short SHA, `latest`, dirty-worktree marker, or `locator@revision` in place of a commit. Components in one repository may use different commits; the formal runner verifies that each source checkout matches each declared path at its commit.

## Benchmark spec

Use for a research question that will be answered by multiple Experiment specs:

```yaml
id: benchmark-<stable-id>
question: <comparison question>
dataset_setups: [dataset-setup-<id>]
metrics: [metric-<id>]
baselines: [<experiment family or fixed model revision>]
seeds: [0, 1, 2]
aggregation:
  statistic: mean_and_std
  confidence_or_uncertainty: <rule>
selection_rules:
  primary_metric: <metric>
  direction: higher | lower
  tie_breakers: [<rule>]
resource_budget: {gpu_hours: <bound>, wall_clock_hours: <bound>}
report_outputs: [tables, plots, qualitative_examples, limitations]
```

## Experiment spec

Use for one concrete model/configuration/seed and one or both independent phases:

```yaml
id: experiment-<stable-id>
status: draft | confirmed
mode: train-evaluate | evaluation-only
benchmark: benchmark-<id>
component_revisions:
  model:
    repository: <Git remote or repository identifier>
    commit: <40-character SHA>
    paths: [<model paths>]
  dataset_setup:
    repository: <Git remote or repository identifier>
    commit: <40-character SHA>
    paths: [<setup paths>]
  benchmark:
    repository: <Git remote or repository identifier>
    commit: <40-character SHA>
    paths: [<benchmark paths>]
  training_config:
    repository: <Git remote or repository identifier>
    commit: <40-character SHA>
    paths: [<config and launcher paths>]
  evaluation_config:
    repository: <Git remote or repository identifier>
    commit: <40-character SHA>
    paths: [<evaluation paths>]
  dependencies:
    repository: <Git remote or repository identifier>
    commit: <40-character SHA>
    paths: [<lockfile paths>]
dataset_setup: dataset-setup-<id>
seed: <integer>
resource_request:
  gpu_count: <positive integer>
  allowed_indices: [<GPU index>]
  min_free_memory_mb: <integer>
  queue_timeout_seconds: <number>
outputs: {checkpoint: <path or locator>, metrics: <path or locator>}
phases:
  - name: train
    command: <target-project command>
    inputs: [train]
  - name: evaluate
    command: <target-project command>
    checkpoint: <path or external checkpoint reference>
    dataset_input: test | validation | <custom input>
    allow_partial_train: false
```

For `evaluation-only`, omit `train`, include an External checkpoint record and a non-empty compatibility declaration covering architecture/model revision, format, preprocessing, shape/dtype, labels, and source conditions. Mark training configuration as `not_applicable` only when the evaluation record explains why; the model, Dataset setup, Benchmark, evaluation configuration, and Experiment spec still require Git commits.

Do not start execution from this skill. Show component revisions, phase inputs, resource plan, and confirmation status. Once the Experiment spec is confirmed, commit that file and record its commit as the Experiment control commit before handing it to `$run-experiment`, `$train-experiment`, or `$evaluate-experiment` for a direct phase request.
