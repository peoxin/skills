---
name: analyze-literature
description: Analyze selected papers and resources with source locators, implementation details, limitations, and review-ready synthesis.
---

# Analyze Literature

Analyze selected, inspectable sources. Separate reported facts, derivations, your inference, conflicts, and missing details. Bind every material claim to a page, section, table, figure, equation, code path, commit, or URL anchor.

## Per-source analysis

Use this Markdown structure:

```markdown
# <Source title>

Source record: <id or link>
Version analyzed: <version>

## Problem and assumptions
## Claimed contributions
## Method and mechanism
## Equations or algorithm
## Data and preprocessing
## Training details
## Evaluation protocol
## Results and evidence locators
## Implementation mapping
## Limitations and threats to validity
## Reproduction questions
## Fact / inference / unknown table
```

For implementation mapping, identify inputs, outputs, shapes, losses, optimization, initialization, checkpoint conventions, and code/config references. Mark missing details instead of filling them from habit.

For a literature review, synthesize selected analyses rather than search snippets. Compare terminology, assumptions, datasets, baselines, metrics, compute, and evidence quality. Keep disagreements visible and label cross-source conclusions as synthesis or inference.

Do not silently convert an analysis into code changes or a scientific conclusion. Hand implementation work to `$implement-model` and hypotheses to `$propose-improvements`.
