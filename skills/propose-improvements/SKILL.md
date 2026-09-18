---
name: propose-improvements
description: Propose evidence-linked model or training improvements from literature, project knowledge, and experiment reports.
---

# Propose Improvements

Use selected Source analyses, Dataset setups, Benchmark/Experiment specs, execution contexts, and Report bundles. Use `$research` when a proposal depends on a current external fact and `$analyze-literature` for source-level reading.

Write one falsifiable proposal at a time:

```markdown
# Proposal: <short name>

## Evidence
- Source or experiment record: <locator>
- Reported fact: <what is directly supported>
- Inference: <what follows and why>

## Mechanism hypothesis
<why the change should affect the target behavior>

## Baseline-relative prediction
<metric, direction, expected effect, and uncertainty>

## Implementation sketch
<model/training/evaluation changes and the repository, full 40-character commit SHA, and paths that would fix each component for a formal experiment>

## Required comparison and ablations
<baseline, controls, seeds, Dataset setup, metric, and stopping condition>

## Risks and failure criteria
<ways the hypothesis can be wrong, compute cost, leakage or confounds>
```

Distinguish reported fact, inference, and new hypothesis visibly. Do not call a proposal an improvement until a confirmed Experiment spec tests it. Do not modify a formal Benchmark spec or launch a run from this entry; pass an approved proposal to `$implement-model` and `$define-experiment`.
