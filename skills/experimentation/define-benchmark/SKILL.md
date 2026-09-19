---
name: define-benchmark
description: Define or revise a self-contained deep-learning Benchmark from committed Dataset setup revisions, including input composition, metric implementations, aggregation, and optional visualizations. Use for reusable evaluation protocols, not concrete model runs.
---

# Define Benchmark

Define one reusable data-and-metric protocol at a time. A Benchmark maps one or more committed Dataset setup revisions into named inputs and fixes the metrics used to interpret them. It does not select a model, baseline, seed, checkpoint, resource request, or concrete run.

## Preconditions

Read every referenced Dataset setup and its component commit. Each setup must contain a consistent `SETUP.md`, structured records, and implementation; be identified by a repository, a complete 40-character commit SHA, and paths; and contain no unresolved input semantics required by the Benchmark. If a setup is missing or must change, stop and hand the work to `$dataset-setup`; resume only after the complete setup revision is committed.

## Component directory

Create or modify only the selected Benchmark directory:

```text
benchmarks/
  <benchmark-id>/
    BENCHMARK.md
    benchmark.yaml
    metrics/
    visualizations/
```

Keep every metric implementation and Benchmark-specific visualization inside this directory. Each Benchmark is self-contained; copy and maintain any needed implementation locally instead of introducing shared metric or evaluation code.

## Align the Benchmark specification

Before creating or normatively changing a Benchmark, invoke `$grill-with-docs`. If it is unavailable, stop. Work the design tree until its frontier is empty, present the complete shared understanding, and wait for explicit user confirmation before writing `BENCHMARK.md`, `benchmark.yaml`, or implementation files.

Keep `BENCHMARK.md` brief and use these sections:

- **Task and scope**: the behavior being evaluated and explicit exclusions.
- **Inputs and composition**: selected Dataset setup inputs, their roles, mappings, and composition rules.
- **Metrics and aggregation**: metric meaning, direction, reduction, and uncertainty treatment.
- **Qualitative outputs**: required examples or visualizations and what they demonstrate.
- **Validation**: implementation checks, acceptance evidence, and failure conditions.

`BENCHMARK.md` is the human-readable contract. `benchmark.yaml` holds exact machine-consumed fields and must remain consistent with it without copying the full YAML into Markdown.

## Benchmark spec

Use this shape:

```yaml
id: benchmark-<stable-id>
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

## Implement and fix the revision

After alignment, modify only `benchmarks/<benchmark-id>/`. Define each metric's direction, units, inputs, masking, reduction, and output semantics. Implement it inside the Benchmark directory and run focused tests or a low-cost smoke test. Every referenced Dataset setup must be a committed component revision, all input and composition rules must be resolved, and every required metric implementation must pass its checks. A required visualization must also be implemented and checked; otherwise remove it from the specification.

If implementation requires a change to scope, inputs, composition, metrics, aggregation, qualitative outputs, validation, or failure conditions, pause and repeat `$grill-with-docs`; formatting and mechanical changes that preserve the specification do not require another interview.

Verify that `BENCHMARK.md`, `benchmark.yaml`, implementations, and tests agree. Show `git diff`, the exact files to commit, and a proposed commit message. Do not create the commit. The Benchmark becomes a fixed component revision only after the user commits the complete Benchmark directory and records the full SHA. Hand that committed revision to `$define-experiment` when the user wants a concrete experiment.
