---
name: analyze-references
description: Analyze selected Reference records and their artifacts with evidence locators, implementation details, limitations, comparisons, and review-ready synthesis.
---

# Analyze References

Analyze selected, inspectable Reference records created by `$add-references`. Read each `reference.yaml` and its retained or external artifacts. Separate reported facts, derivations, your inference, conflicts, and missing details. Bind every material claim to a page, section, table, figure, equation, code path, artifact path, commit, or URL anchor.

This early collection uses the current co-located Reference format only. It does not migrate or promise compatibility with superseded `source-*`, flat YAML, or status-bearing records.

## Reference collection

Read the target project's instructions, root `CONTEXT.md` or `CONTEXT-MAP.md`, applicable ADRs, and the existing Reference collection. Continue using one clearly established analysis location without renaming or migrating it; when several locations are plausible, ask the user to choose. If no relevant collection exists, use `references/analysis/`.

Reference records live at `<class>/<stable-id>/reference.yaml`. Retained artifacts are next to the record. External artifacts must be inspected from the recorded path or from an external checkout fixed to the recorded commit; do not copy a complete third-party repository into the Reference collection.

Write a single-Reference analysis as `analysis/<reference-id>.md` and a cross-Reference review as `analysis/review-<topic>.md` when using the default collection. Keep each Reference record separate from its analyses so it can support multiple interpretations without changing identity.

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

For implementation mapping, identify inputs, outputs, shapes, losses, optimization, initialization, checkpoint conventions, and code or configuration references. Use artifact paths, source locators, and recorded commits as evidence locators. Mark missing details in the analysis instead of filling them from habit or rewriting the Reference record.

For a review, synthesize selected analyses rather than search snippets. Compare terminology, assumptions, datasets, implementations, baselines, metrics, compute, and evidence quality. Keep disagreements visible and label cross-Reference conclusions as synthesis or inference.

Do not silently convert an analysis into code changes or a scientific conclusion. Hand implementation work to `$implement-model` and hypotheses to `$propose-improvements`.
