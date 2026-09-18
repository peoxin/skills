---
name: evaluate-experiment
description: Execute or analyze an experiment evaluation phase and produce traceable metrics, visualizations, and human-readable reports.
---

# Evaluate Experiment

This entry owns the `evaluate` phase and its report bundle. It can be called directly for an existing checkpoint or delegated to by `$run-experiment`. It does not schedule a train phase or decide the parent experiment's phase order.

Read the Experiment spec, Dataset setup, checkpoint record, metric identity, and execution context. Never treat a missing, failed, cancelled, or partial phase as zero or success. When called by `$run-experiment`, use the supplied run ID, assigned devices, cancellation state, and output locations without allocating a second GPU reservation.

## Compatibility and metrics

Before formal evaluation, check model architecture/revision, checkpoint digest and format, input shape and dtype, preprocessing, label mapping, device constraints, source/license, and the selected Dataset input. Record each check as pass, mismatch, or unknown.

Keep metric identity separate from a metric value:

```yaml
name: <metric>
direction: higher | lower
units: <unit>
inputs: [<prediction/target fields>]
masking: <rule>
reduction: <rule>
output_semantics: <meaning>
implementation_revision: git:<commit> | locator@revision
```

## Canonical report data

Write structured data first:

```yaml
id: report-<run-or-benchmark-id>
title: <report title>
inputs:
  experiment: <id>
  execution_context: <id>
  dataset_setup: <id>
phases:
  - name: train
    status: completed | failed | cancelled | blocked | partial
metrics:
  - name: <metric>
    value: <number or null>
    split: <Dataset setup input>
    source: <metric record and manifest locator>
aggregation: {statistic: <rule>, uncertainty: <rule>}
figures:
  - name: <figure>
    path: <path or locator>
    status: available | not_applicable | failed
missing: [<missing input, failed phase, or unresolved interpretation>]
```

Render a Markdown or HTML view from this data. Include phase status, inputs, aggregation/statistics, metrics, figure links, compatibility results, failures, and missingness. Optional project-provided visualization hooks may add qualitative examples and error cases; they must record their input and revision and may not rewrite canonical metrics.

Return an evaluation phase manifest linking the checkpoint, Dataset input, metric records, report data, rendered report, figures, execution context, assigned GPUs, and final status. `$run-experiment` uses this manifest when writing the parent run manifest.
