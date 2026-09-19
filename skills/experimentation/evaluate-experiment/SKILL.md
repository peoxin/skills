---
name: evaluate-experiment
description: Execute or analyze an experiment evaluation phase and produce traceable metrics, visualizations, and human-readable reports.
---

# Evaluate Experiment

This entry owns the `evaluate` phase and its report bundle. It can be called directly for an existing checkpoint or delegated to by `$run-experiment`. It does not schedule a train phase or decide the parent experiment's phase order.

Unless the Experiment spec declares another location, write the evaluation report bundle under `results/<experiment-id>/<result-id>/`.

Read the fixed Experiment revision, the Benchmark's selected Dataset derivation inputs, checkpoint record, Benchmark-owned metric identities and implementations, and execution context. Never treat a missing, failed, cancelled, or partial phase as zero or success. When called by `$run-experiment`, read and follow the supplied `EXECUTION.md`, then use the supplied run ID, assigned devices, cancellation state, and output locations without allocating a second GPU reservation.

Before direct evaluation, require the Experiment control commit and the Git component preflight: consistent Experiment files, clean source paths, complete component SHAs, existing commits, and current checkout paths matching each declared commit. When delegated, use the orchestrator's verified context and re-check it at evaluation start.

For a direct invocation, apply `$run-experiment`'s execution-specification workflow before starting. `$grill-with-docs` is required; if it is unavailable, stop. Write `results/<experiment-id>/<result-id>/EXECUTION.md` with the bound Experiment revision, commands and resources, output locations, cancellation and failure handling, and verification. If evaluation requires a normative change to that specification, pause and repeat `$grill-with-docs` before continuing.

## Compatibility and metrics

Before formal evaluation, check model architecture/revision, checkpoint digest and format, input shape and dtype, preprocessing, label mapping, device constraints, source/license, and every selected Benchmark input. Record each check as pass, mismatch, or unknown.

Keep metric identity separate from a metric value:

```yaml
name: <metric>
direction: higher | lower
units: <unit>
inputs: [<prediction/target fields>]
masking: <rule>
reduction: <rule>
output_semantics: <meaning>
implementation_paths: [benchmarks/<benchmark-id>/metrics/<metric implementation paths>]
```

## Canonical report data

Write structured data first:

```yaml
id: report-<run-or-benchmark-id>
title: <report title>
inputs:
  experiment: <id>
  execution_context: <id>
  dataset_derivations: [<dataset-derivation-id>]
  benchmark_inputs: [<named Benchmark inputs>]
experiment_spec_commit: <40-character SHA>
component_commits:
  model: {repository: <id>, commit: <40-character SHA>, paths: [<paths>]}
  dataset_derivations:
    - id: <dataset-derivation-id>
      repository: <id>
      commit: <40-character SHA>
      paths: [<paths>]
  benchmark: {repository: <id>, commit: <40-character SHA>, paths: [<paths>]}
  dependencies: {repository: <id>, commit: <40-character SHA>, paths: [<paths>]}
phases:
  - name: train
    status: completed | failed | cancelled | blocked | partial
metrics:
  - name: <metric>
    value: <number or null>
    split: <Dataset derivation input>
    source: <metric record and manifest locator>
aggregation: {statistic: <rule>, uncertainty: <rule>}
figures:
  - name: <figure>
    path: <path or locator>
    status: available | not_applicable | failed
missing: [<missing input, failed phase, or unresolved interpretation>]
```

Render a Markdown or HTML view from this data. Include phase status, inputs, aggregation/statistics, metrics, figure links, compatibility results, failures, and missingness. Optional project-provided visualization hooks may add qualitative examples and error cases; they must record their input and revision and may not rewrite canonical metrics.

Return an evaluation phase manifest linking the checkpoint, Benchmark inputs, metric records, report data, rendered report, figures, execution context, assigned GPUs, and final status. `$run-experiment` uses this manifest when writing the parent Result manifest.
