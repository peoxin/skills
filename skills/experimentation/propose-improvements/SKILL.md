---
name: propose-improvements
description: Propose evidence-linked model or training improvements from Reference analyses, project knowledge, and experiment reports.
---

# Propose Improvements

Use selected Reference analyses, Dataset derivations, Benchmark/Experiment specs, and declared Experiment outputs. Use `$research` when a proposal depends on a current external fact and `$analyze-references` for Reference-level reading.

Write one falsifiable proposal at a time:

```markdown
# Proposal: <short name>

## Evidence
- Reference or experiment record: <locator>
- Reported fact: <what is directly supported>
- Inference: <what follows and why>

## Mechanism hypothesis
<why the change should affect the target behavior>

## Baseline-relative prediction
<metric, direction, expected effect, and uncertainty>

## Implementation sketch
<model/Benchmark/Experiment changes and the repository, full 40-character commit SHA, and paths that would fix each component for a formal experiment>

## Required comparison and ablations
<baseline, controls, seeds, Dataset derivation, metric, and stopping condition>

## Risks and failure criteria
<ways the hypothesis can be wrong, compute cost, leakage or confounds>
```

Distinguish reported fact, inference, and new hypothesis visibly. Do not call a proposal an improvement until a fixed Experiment revision tests it. Do not modify a formal Benchmark spec or launch a run from this entry. Pass an approved model change to `$implement-model`, a protocol change to `$define-benchmark`, and a concrete test design to `$define-experiment` after its Benchmark revision is committed.
