---
name: implement-model
description: Reproduce an existing model or implement a new PyTorch idea with an explicit evidence boundary, tests, and a confirmed integration plan.
---

# Implement Model

Use one of two intents:

- `reproduce`: implement an existing method from selected papers, repositories, configurations, or checkpoints.
- `new-method`: implement a user-proposed mechanism or a confirmed improvement proposal.

If intent is unclear, ask. Inspect the target project's instructions, architecture, data interfaces, training entry points, tests, and dependency conventions before proposing edits.

## Plan before edits

Present:

1. Intent and success criteria.
2. Evidence boundary: source-backed behavior versus user hypothesis or your inference.
3. Model interface: inputs, outputs, shapes, dtype/device behavior, train/eval behavior, and state/checkpoint keys.
4. Target files and integration points.
5. Key PyTorch code as Markdown code blocks when it helps review the design.
6. Tests, smoke test, expected outputs, and known deviations.
7. Components that must receive fixed revisions for a formal experiment.

For reproduction, maintain a deviation table:

```markdown
| Aspect | Source behavior | Proposed implementation | Status |
| --- | --- | --- | --- |
| Architecture | ... | ... | exact / approximated / unknown |
```

For a new method, record the mechanism, baseline-relative prediction, falsifiable failure condition, required ablations, and compute implications.

After user confirmation, edit only the target project, run focused tests and a low-cost smoke test, and summarize the resulting revision. Do not silently change the Dataset setup, Benchmark spec, or research question.
