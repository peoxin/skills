---
name: implement-model
description: Reproduce an existing model or implement a new PyTorch idea from an aligned model specification with an explicit evidence boundary and tests.
---

# Implement Model

Use one of two intents:

- `reproduce`: implement an existing method from selected papers, repositories, configurations, or checkpoints.
- `new-method`: implement a user-proposed mechanism or a confirmed improvement proposal.

If intent is unclear, ask. Inspect the target project's instructions, architecture, data interfaces, training entry points, tests, and dependency conventions before proposing edits.

## Default directory

Place each logical model in a self-contained root-level directory:

```text
models/
  <model-id>/
    MODEL.md
    model.py
    config.yaml
    tests/
```

The directory name is not a version; formal runs record its repository, full commit, and paths. Do not create or require `common/`, `lib/`, `_shared/`, or another shared model-code directory. If two models need similar code, copy the relevant implementation into each model directory and maintain each copy independently.

## Align the model specification

Before creating or normatively changing a Model, invoke `$grill-with-docs`. If it is unavailable, stop. Work the design tree until its frontier is empty, present the complete shared understanding, and wait for explicit user confirmation before writing `MODEL.md` or editing implementation files.

Keep `MODEL.md` brief and use these sections:

- **Intent and evidence boundary**: success criteria and the separation between source-backed behavior, user hypotheses, and inference.
- **Interface**: inputs, outputs, shapes, dtype/device behavior, train/eval behavior, and state or checkpoint keys.
- **Required behavior**: architecture and behavior the implementation must preserve.
- **Integration**: target files, dependencies, callers, and other components that require fixed revisions.
- **Verification and known deviations**: tests, smoke tests, expected outputs, failure conditions, and accepted departures from source behavior.

`MODEL.md` is the human-readable contract. Keep it consistent with code and machine-readable configuration without copying the complete configuration into Markdown. Key PyTorch code may appear as Markdown code blocks when it materially helps review the contract.

For reproduction, maintain this deviation table in `MODEL.md`:

```markdown
| Aspect | Source behavior | Proposed implementation | Status |
| --- | --- | --- | --- |
| Architecture | ... | ... | exact / approximated / unknown |
```

For a new method, record the mechanism, baseline-relative prediction, falsifiable failure condition, required ablations, and compute implications in `MODEL.md`.

After writing the aligned specification, edit only the target project and run focused tests and a low-cost smoke test. If implementation requires a change to behavior, interfaces, integration, verification, or failure conditions, pause and repeat `$grill-with-docs`; formatting and mechanical changes that preserve the specification do not require another interview. Do not silently change the Dataset setup, Benchmark spec, or research question.

Verify that `MODEL.md`, code, configuration, and tests agree. Show `git diff`, the exact files to commit, and a proposed commit message. This skill does not create the commit. A formal run may consume the Model only after the user commits the complete Model directory and records its repository, full SHA, and paths in the Experiment spec.
