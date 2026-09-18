---
name: evaluate-experiment
description: Evaluate experiment outputs and produce traceable machine-readable data plus human-readable quantitative and qualitative reports.
---

# Evaluate Experiment

Read the Experiment spec, Dataset setup, phase manifests, checkpoint record, metric identity, and execution context. Never treat a missing, failed, cancelled, or partial phase as zero or success.

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
