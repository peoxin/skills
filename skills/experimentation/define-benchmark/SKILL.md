---
name: define-benchmark
description: Define a reusable Benchmark component that evaluates model behavior with selected Dataset derivations, quantitative metrics, and qualitative visualizations.
---

# Define Benchmark

Treat each Benchmark as a self-contained, reusable evaluation protocol. A Benchmark selects inputs from one or more Dataset derivations, defines the behavior being evaluated, and fixes the quantitative metrics and qualitative visualizations used to interpret results. It does not select a model, baseline, seed, checkpoint, resource request, or concrete experiment run.

## Workflow

1. Inspect the target project's instructions, Dataset derivations, data interfaces, evaluation conventions, and dependency conventions.
2. Before defining or changing a Benchmark, invoke `$grill-with-docs`. Resolve the task, Dataset inputs, input composition, quantitative metrics, and qualitative visualizations. Continue only after the design is explicit and confirmed. If it is unavailable, stop.
3. Create or update `BENCHMARK.md` inside `benchmarks/<benchmark-id>/`.
4. Implement the Benchmark inside the selected `benchmarks/<benchmark-id>/` directory. Keep metric and visualization implementations self-contained within the Benchmark.
5. Keep `BENCHMARK.md` consistent with the metric and visualization implementations. If the task, Dataset inputs, composition, metrics, aggregation, visualizations, or implementation contract changes, pause and repeat `$grill-with-docs` before continuing.
6. List the changed files and show the diff. Do not create a commit.

## Benchmark Directory

Use one self-contained directory for each logical Benchmark:

```text
benchmarks/
  <benchmark-id>/
    BENCHMARK.md
    metrics/
    visualizations/
    <implementation files>
```

Keep every Benchmark-specific metric and visualization implementation inside this directory. The Benchmark may contain other implementation files, configurations, and tests as required by the project. Do not introduce shared metric or evaluation directories for the Benchmark; keep its implementation self-contained.

## BENCHMARK.md

`BENCHMARK.md` is the human-readable contract for the Benchmark. Keep it concise and consistent with the implementation.

Use these sections:

### Description

Describe the behavior being evaluated, the Benchmark's purpose, and its explicit boundaries. State what the Benchmark does not evaluate when that boundary matters.

### Dataset

Identify the Dataset derivations and named inputs used by the Benchmark. Describe each input's role, mappings, selection boundaries, and composition rules. Describe the data contract the metric and visualization implementations consume.

### Metrics

Define the quantitative metrics. For each metric, record its meaning, direction, units, prediction and target inputs, masking, reduction, aggregation, uncertainty treatment, and output semantics when applicable.

### Visualizations

Define required qualitative visualizations or example outputs. Record the inputs, output form, and behavior each visualization is intended to demonstrate. Omit this section when the Benchmark has no qualitative output.

### Implementation

Record implementation files and dependencies when they are needed to understand or reproduce the Benchmark. Keep metric and visualization code in the Benchmark directory. The specification must remain consistent with the actual implementation. If implementation changes the Benchmark's task, Dataset inputs, composition, metrics, aggregation, visualizations, or other contract, stop and realign the design before continuing.
