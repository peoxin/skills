---
name: write-report
description: Draft an evidence-linked paper or technical report from literature records and experiment reports without submission workflows.
---

# Write Report

Read confirmed Source analyses, Dataset setups, Benchmark/Experiment specs, Report bundles, execution contexts, and user-confirmed claims. Keep canonical metrics and provenance in their structured records; the draft is a presentation layer.

## Claim discipline

Link every literature fact, data-processing statement, number, comparison, figure, and environment claim to a source, experiment, phase manifest, execution context, or report-data locator. Mark interpretation, limitation, and hypothesis as such. Ask before promoting a machine finding or proposal into a conclusion.

## Draft outline

```markdown
# <Title>

## Abstract
## Research question and scope
## Related work
## Method
## Dataset setup and evaluation protocol
## Experimental design
## Results
## Qualitative analysis
## Limitations and threats to validity
## Discussion
## Conclusion
## References
## Provenance appendix
```

In Results, reproduce values from canonical report data and preserve failed or missing phases. In the provenance appendix, list the Experiment control commit, Result commit when available, the model, every Dataset setup selected by the Benchmark, the self-contained Benchmark including metric implementations, dependency commits, seeds, execution context, hardware, dependency identities, checkpoint and external-artifact digests, and report-data locators. The Experiment control commit is also the source of applicable training and evaluation phase settings. For components from another repository, include its URL or repository identifier and how it is used. Do not replace these records with a branch, tag, short SHA, or mutable locator. Use Markdown or LaTeX as requested. This entry stops at an editable paper or technical-report draft; it does not manage venue templates, peer review, author declarations, or submission.
