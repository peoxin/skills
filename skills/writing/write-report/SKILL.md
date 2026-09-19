---
name: write-report
description: Draft an evidence-linked report or paper from Reference analyses and experiment results without submission workflows.
---

# Write Report

Read Reference records and analyses, Dataset setups, Benchmark/Experiment specs, Report bundles, execution contexts, and user-confirmed claims. Keep canonical metrics and provenance in their structured records; the draft is a presentation layer and must not rewrite experiment records.

## Writing directory

Read the target project's instructions and existing reporting, paper, or writing directories. Continue using one clearly established location without renaming or migrating it. If several directories could own the document, show the candidates and ask the user to choose.

When no relevant directory exists, create `writing/<writing-id>/` after the user confirms the target. Treat every immediate child of `writing/` as one writing project regardless of whether it is a report, paper, note, or another form. Make no assumption about its main filename, document type, internal directories, or supporting files; follow the user's request and the target project's conventions.

## Claim discipline

Link every Reference-derived fact, data-processing statement, number, comparison, figure, and environment claim to a Reference, experiment, phase manifest, execution context, or report-data locator. Mark interpretation, limitation, and hypothesis as such. Ask before promoting a machine finding or proposal into a conclusion.

## Document structure

Follow an existing document structure or the user's requested structure. When neither exists, clarify the document's purpose, audience, claims, and required evidence, then agree an outline with the user before writing. Do not assume that different Writing projects share filenames, sections, formats, or supporting files.

In Results, reproduce values from canonical report data and preserve failed or missing phases. In the provenance appendix, list the Experiment control commit, Result commit when available, the model, every Dataset setup selected by the Benchmark, the self-contained Benchmark including metric implementations, dependency commits, seeds, execution context, hardware, dependency identities, checkpoint and external-artifact digests, and report-data locators. The Experiment control commit is also the source of applicable training and evaluation phase settings. For components from another repository, include its URL or repository identifier and how it is used. Do not replace these records with a branch, tag, short SHA, or mutable locator. Use Markdown or LaTeX as requested. This entry stops at an editable paper or technical-report draft; it does not modify canonical Reference or experiment records, manage venue templates, peer review, author declarations, or submission.
