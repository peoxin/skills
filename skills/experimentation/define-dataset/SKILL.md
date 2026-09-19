---
name: define-dataset
description: Define a self-contained, reproducible Dataset component, including its source revision and the rules that derive usable inputs.
---

# Define Dataset

Treat each Dataset as an independent, reproducible data component. A Dataset revision identifies the source content; a Dataset derivation defines the rules that turn that revision into named inputs for training, validation, testing, or other declared uses.

## Workflow

1. Inspect the target project's instructions, data directories, existing Dataset records, data interfaces, and dependency conventions.
2. Before defining or changing a Dataset, invoke the `grill-with-docs` skill. Resolve the Dataset revision, required named inputs, applicable derivation rules, data boundaries, and implementation constraints. If it is unavailable, stop.
3. Reuse the existing `REVISION.md` when it matches the required raw Dataset revision. If it is missing, incomplete, or does not match, stop and tell the user what must be resolved before continuing. Create a new `REVISION.md` only for a new Dataset directory. Then create or update the selected `DERIVATION.md`.
4. Implement the derivation inside the selected `data/<dataset-id>/derivations/<derivation-id>/` directory. Keep its data processing code and configuration self-contained.
5. Keep `REVISION.md` and `DERIVATION.md`, implementation files, and configuration consistent. If the Dataset revision, derived data, derivation rules, or label semantics change, pause and repeat the `grill-with-docs` skill before continuing.
6. List the changed files and show the diff. Do not create a commit.

## Dataset Directory

Use one self-contained root-level directory for each logical Dataset:

```text
data/
  <dataset-id>/
    REVISION.md
    derivations/
      <derivation-id>/
        DERIVATION.md
        configs/
          <preset>.yaml
        <implementation files>
```

One `data/<dataset-id>/` directory represents one selected raw Dataset revision. Multiple derivations may reuse its `REVISION.md`. The optional `configs/` directory may contain multiple reusable derivation presets. Keep all derivation implementation code inside the corresponding `<derivation-id>/` directory, with no required filename or subdirectory.

Do not create or require shared data-processing directories such as `common/`, `lib/`, `_shared/`, or top-level processing directories. If derivations need similar code, keep each derivation self-contained and maintain copies independently.

Do not create `dataset.yaml` or `derivation.yaml`. Git fixes the complete component version; later Experiments record the relevant commit and paths.

## REVISION.md

`REVISION.md` describes the selected raw Dataset revision shared by its derivations. Keep it consistent with the source and with every derivation that consumes it. If the revision is missing, incomplete, or does not match the requested derivation, stop and tell the user what must be resolved.

Use these sections:

### Description

Describe the Dataset's source and role in the experiment, without describing any derived inputs.

### Dataset Source

Record the source locator, publisher, source version or release, acquisition details, content identity or digest, license, and access conditions.

### Raw Data

Describe the raw files, fields, sample structure, labels, and availability exposed by the source.

### Known Constraints

Record source-defined limitations, missing content, access restrictions, licensing conditions, and other facts that derivations must respect.

## DERIVATION.md

`DERIVATION.md` is the human-readable contract for one Dataset derivation. Keep it consistent with the implementation and configuration without copying complete configuration or source code into it.

Use these sections:

### Description

Describe the purpose and boundaries of this derivation.

### Dataset Derivation

Identify the existing `REVISION.md`, describe the Raw Data used by this derivation, and record the ordered rules that transform or select it. Reuse the revision; do not copy or redefine it. Record filtering, sampling, preprocessing, augmentation, label mapping, and other transformations when applicable, along with relevant seeds and configuration choices.

### Derived Data

Describe the named data produced by this derivation for training, evaluation, or other declared uses. For each one, record its purpose, selection boundary, format, fields, label meaning, and derived identity when applicable. Record only the data and properties that apply to this Dataset.

### Implementation

Record implementation files, configurations, dependencies, generation commands, materialized or cached artifacts, and choices that affect reproducibility. Keep the code itself in the implementation files.

The specification must remain consistent with the actual implementation. If the implementation changes the Dataset revision, derived data, derivation rules, or label semantics, stop and realign the design before continuing.
