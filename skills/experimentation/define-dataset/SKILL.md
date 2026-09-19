---
name: define-dataset
description: Define a self-contained, reproducible Dataset component, including its source revision and the rules that derive usable inputs.
---

# Define Dataset

Treat each Dataset as an independent, reproducible data component. A Dataset revision identifies the source content; a Dataset derivation defines the rules that turn that revision into named inputs for training, validation, testing, or other declared uses.

## Workflow

1. Inspect the target project's instructions, data directories, existing Dataset records, data interfaces, and dependency conventions.
2. Before defining or changing a Dataset, invoke `$grill-with-docs`. Resolve the Dataset revision, required named inputs, applicable derivation rules, data boundaries, and implementation constraints. If it is unavailable, stop.
3. Reuse the existing `REVISION.md` when it matches the required raw Dataset revision. If it is missing, incomplete, or does not match, stop and tell the user what must be resolved before continuing. Create a new `REVISION.md` only for a new Dataset directory. Then create or update the selected `DERIVATION.md`.
4. Implement the required processing code and configuration inside the Dataset and derivation directories.
5. Run the validations required by `DERIVATION.md` and confirm that every declared input, derivation rule, and derived result agrees with the implementation.
6. Keep `REVISION.md`, `DERIVATION.md`, implementation files, and configuration consistent. If the Dataset revision, inputs, derivation rules, label semantics, or validation requirements change, pause and repeat `$grill-with-docs` before continuing.
7. List the changed files and show the diff. Do not create a commit.

Apply only the rules relevant to the Dataset. A Dataset need not define splits, sampling, labels, augmentation, or derived-input digests unless it uses them.

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

One `data/<dataset-id>/` directory represents one selected raw Dataset revision. Multiple derivations may reuse its `REVISION.md`. A different source version is a different Dataset component; do not replace or silently revise an existing revision to support a new derivation. The optional `configs/` directory may contain multiple reusable derivation presets. Keep all derivation implementation code inside the corresponding `<derivation-id>/` directory, with no required filename or subdirectory.

Do not create or require shared data-processing directories such as `common/`, `lib/`, `_shared/`, or top-level processing directories. If derivations need similar code, keep each derivation self-contained and maintain copies independently.

Do not create `dataset.yaml` or `derivation.yaml`. Git fixes the complete component version; later Experiments record the relevant commit and paths.

## REVISION.md

`REVISION.md` describes the selected raw Dataset revision shared by its derivations. Keep it consistent with the source and with every derivation that consumes it. If the revision is missing, incomplete, or does not match the requested derivation, stop and tell the user what must be resolved.

Use these sections:

### Purpose

Describe the Dataset's source and role in the experiment, without describing any derived inputs.

### Source

Record the source locator, source version or release, content identity or digest, license, access conditions, and provenance.

### Raw Interface

Describe the raw files, fields, sample structure, labels, and availability exposed by the source.

### Known Constraints

Record source-defined limitations, missing content, access restrictions, licensing conditions, and other facts that derivations must respect.

## DERIVATION.md

`DERIVATION.md` is the human-readable contract for one Dataset derivation. Keep it consistent with the implementation and configuration without copying complete configuration or source code into it.

Use these sections:

### Purpose

Describe the purpose and boundaries of this derivation.

### Dataset Revision

Identify the `REVISION.md` and source facts from which this derivation is built. Reuse the existing revision; do not copy or redefine it in the derivation.

### Inputs

Describe each named input, its purpose, source selection, split semantics, format, fields, label meaning, and derived identity. Record only the inputs and properties that apply to this Dataset.

Create a new derivation directory for a new purpose, input set, or processing rule. Update an existing derivation only when the user has explicitly chosen that derivation for revision.

### Derivation

Describe the ordered rules that produce each input, including filtering, sampling, preprocessing, augmentation, label mapping, and other transformations when applicable. Record relevant seeds and configuration choices.

### Validation

Describe the properties that must hold and the failure conditions for this derivation. Include only applicable checks, such as input availability, split leakage, label transformations, random sampling, and derived-input identity.

### Implementation

Record implementation files, configurations, dependencies, generation commands, materialized or cached artifacts, and choices that affect reproducibility. Keep the code itself in the implementation files.

The specification must remain consistent with the actual implementation. If the implementation changes the Dataset revision, inputs, derivation rules, or validation requirements, stop and realign the design before continuing.
