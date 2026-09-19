---
name: implement-model
description: Implement a self-contained, reproducible PyTorch model component from an aligned model design.
---

# Implement Model

Treat each model as an independent, reproducible component of a deep-learning experiment. Keep its architecture, behavior, implementation, and configuration understandable and self-contained.

## Workflow

1. Inspect the target project's instructions, architecture, data interfaces, training entry points, and dependency conventions.
2. Before designing or changing the model, invoke `$grill-with-docs`. Continue until the architecture, module contracts, interfaces, training and inference behavior, and implementation constraints are explicit and confirmed. If it is unavailable, stop.
3. Write `MODEL.md` as the model contract.
4. Implement the model in a self-contained `models/<model-id>/` directory.
5. Keep `MODEL.md`, implementation files, and configuration consistent. If implementation reveals a change to the architecture, module interfaces, input/output behavior, training or inference behavior, or checkpoint contract, pause and repeat `$grill-with-docs` before continuing.

After implementation, list the changed files and show the diff. Do not create a commit.

## Model Directory

Use one self-contained root-level directory for each logical model:

```text
models/
  <model-id>/
    MODEL.md
    configs/
      <preset>.yaml
    <implementation files>
```

The directory name identifies the logical model, not a version. The implementation may contain one or more files, with no required filename or subdirectory. Keep all model implementation code inside `models/<model-id>/`. Do not create or require shared model-code directories such as `common/`, `lib/`, or `_shared/`. If models need similar code, keep each model implementation self-contained and maintain copies independently.

The optional `configs/` directory may contain multiple reusable model configuration presets. Record each preset's purpose and its effects on architecture or behavior in `MODEL.md`; keep training hyperparameters and resource settings in the Experiment configuration.

## MODEL.md

`MODEL.md` is the human-readable contract for the model. Keep it consistent with the implementation and machine-readable configuration without copying the complete configuration or source code into it.

Use these sections:

### Purpose

Describe the model's role in the experiment, its problem boundary, and any source, paper, repository, checkpoint, or design basis needed to understand its architecture and reproduce it.

### Architecture

Describe the complete model data flow, model inputs and outputs, tensor shapes, dtype and device constraints, and behavior in training and inference modes.

Give each meaningful module its own subsection. For every module, describe its responsibility, internal architecture, local inputs and outputs, tensor shapes, parameters and state, and behavior that the implementation must preserve.

### Implementation

Record implementation files, configuration, dependencies, initialization, checkpoint and state-dict details, and implementation choices that affect reproducibility. Keep the code itself in the implementation files.

The specification must remain consistent with the actual implementation. If the implementation changes the model contract, stop and realign the design before continuing.
