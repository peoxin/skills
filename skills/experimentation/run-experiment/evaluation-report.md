# Evaluation report

Use this reference only when the Experiment declares an `Evaluate` phase. The evaluation phase remains part of `$run-experiment`; this file keeps its report-specific procedure out of the main execution workflow.

## Compatibility

Before evaluation, check the Model architecture and revision, checkpoint digest and format, input shape and dtype, preprocessing, label mapping, device constraints, source and license conditions, and every selected Benchmark input. Record each check as `pass`, `mismatch`, or `unknown`. A mismatch blocks formal evaluation; an unknown is recorded in the report and is never treated as a pass.

Use the Benchmark's metric and visualization implementations as the source of truth. Evaluation configuration may control batch size, workers, precision, devices, checkpoint selection, and output locations, but it may not redefine Benchmark metrics, masking, reduction, aggregation, or visualizations.

## Canonical report data

Write structured report data before rendering a human-readable report:

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
compatibility: [<check records>]
missing: [<missing input, failed phase, or unresolved interpretation>]
```

Render Markdown or HTML from the canonical data. Include phase status, inputs, aggregation and uncertainty, metrics, figure links, compatibility results, failures, and missingness. Optional project-provided visualization hooks may add qualitative examples and error cases; record their input and revision and never let them rewrite canonical metrics.

Record the report bundle path in the parent Result manifest along with the checkpoint, Benchmark inputs, metric records, rendered report, figures, execution context, assigned GPUs, and final evaluation status. A failed, cancelled, partial, or blocked Train is never a zero or successful input to evaluation; follow the Experiment's explicit partial-training rule.
