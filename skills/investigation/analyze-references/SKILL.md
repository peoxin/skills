---
name: analyze-references
description: Analyze selected research References with evidence locators, implementation details, limitations, comparisons, and review-ready synthesis.
---

# Analyze References

Analyze selected, inspectable References. Accept current `reference-*` records and legacy `source-*` records. Separate reported facts, derivations, your inference, conflicts, and missing details. Bind every material claim to a page, section, table, figure, equation, code path, commit, or URL anchor.

Read the target project's instructions and existing research-material directories. Continue using one clearly established analysis location without renaming or migrating it; when several locations are plausible, ask the user to choose. If no relevant collection exists, use `references/analysis/`.

Write a single-Reference analysis as `analysis/<reference-id>.md` and a cross-Reference review as `analysis/review-<topic>.md` when using the default collection. Preserve an established project's naming and format. Keep each Reference record separate from its analyses so it can support multiple interpretations without changing identity.

## Per-Reference analysis

Use this Markdown structure:

```markdown
# <Reference title>

Reference record: <id or link>
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

For implementation mapping, identify inputs, outputs, shapes, losses, optimization, initialization, checkpoint conventions, and code or configuration references. Inspect complete third-party repositories from an external checkout fixed to the recorded commit; do not copy the repository into the Reference collection. Mark missing details instead of filling them from habit.

For a review, synthesize selected analyses rather than search snippets. Compare terminology, assumptions, datasets, implementations, baselines, metrics, compute, and evidence quality. Keep disagreements visible and label cross-Reference conclusions as synthesis or inference.

Do not silently convert an analysis into code changes or a scientific conclusion. Hand implementation work to `$implement-model` and hypotheses to `$propose-improvements`.
