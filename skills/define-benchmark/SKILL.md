---
name: define-benchmark
description: Define or revise a self-contained deep-learning Benchmark from confirmed Dataset setups, including input composition, metric implementations, aggregation, and optional visualizations. Use for reusable evaluation protocols, not concrete model runs.
---

# Define Benchmark

Define one reusable data-and-metric protocol at a time. A Benchmark maps one or more confirmed Dataset setups into named inputs and fixes the metrics used to interpret them. It does not select a model, baseline, seed, checkpoint, resource request, or concrete run.

## Preconditions

Read every referenced Dataset setup and its component commit. Each setup must be confirmed, identified by a repository, a complete 40-character commit SHA, and paths, and contain no unresolved input semantics required by the Benchmark. If a setup is missing or must change, stop and hand the work to `$dataset-setup`; resume only after the setup is confirmed and committed.

## Component directory

Create or modify only the selected Benchmark directory:

```text
benchmarks/
  <benchmark-id>/
    benchmark.yaml
    metrics/
    visualizations/
```

Keep every metric implementation and Benchmark-specific visualization inside this directory. Each Benchmark is self-contained; copy and maintain any needed implementation locally instead of introducing shared metric or evaluation code.

## Benchmark spec

Use this shape:

```yaml
id: benchmark-<stable-id>
status: draft | confirmed
task: <task definition>
dataset_setups:
  - id: dataset-setup-<id>
    role: train | validation | test | auxiliary
    repository: <Git remote or repository identifier>
    commit: <40-character SHA>
    paths: [<data/<dataset-id>/setups/<setup-id> paths>]
inputs:
  train: [<Dataset setup input references>]
  validation: [<Dataset setup input references>]
  test: [<Dataset setup input references>]
  custom-input: [<Dataset setup input references>]
composition:
  train: {operation: concat | interleave | join, rule: <composition rule>}
  test: {operation: <operation>, rule: <composition rule>}
metric_inputs:
  <metric-id>: [<named Benchmark inputs>]
metrics:
  - id: <metric-id>
    direction: higher | lower
    units: <unit>
    inputs: [<prediction and target fields>]
    masking: <rule>
    reduction: <rule>
    output_semantics: <meaning>
    implementation_paths: [benchmarks/<benchmark-id>/metrics/<paths>]
aggregation:
  statistic: <rule>
  confidence_or_uncertainty: <rule>
qualitative_outputs:
  - id: <output-id>
    implementation_paths: [benchmarks/<benchmark-id>/visualizations/<paths>]
unavailable_inputs: [<input name and reason>]
```

Omit unused named inputs, composition entries, or qualitative outputs rather than inventing placeholders. The directory name is a logical identity, not a version.

## Implement and confirm

Before editing, show the proposed input mapping, composition rules, metrics, files, tests, and qualitative outputs, then wait for user confirmation. Modify only `benchmarks/<benchmark-id>/`.

Define each metric's direction, units, inputs, masking, reduction, and output semantics. Implement it inside the Benchmark directory and run focused tests or a low-cost smoke test. A Benchmark can become `confirmed` only when all referenced Dataset setups are confirmed and committed, input and composition rules are resolved, every required metric implementation passes its checks, and the user explicitly confirms the protocol. A declared required visualization must also be implemented and checked; otherwise omit it or keep the Benchmark in `draft`.

After confirmation, show `git diff`, the exact files to commit, and a proposed commit message. Do not create the commit. The Benchmark becomes a formal component only after the user commits the complete Benchmark directory and records the full SHA. Hand that confirmed Benchmark commit to `$define-experiment` when the user wants a concrete experiment.
