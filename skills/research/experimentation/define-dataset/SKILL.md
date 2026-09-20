---
name: define-dataset
description: Define a self-contained, reusable Dataset component, including its source revision and the rules that derive usable inputs.
---

# Define Dataset

Treat each **Dataset** as an independent, reusable component. A **Dataset revision** identifies the source content; a **Dataset derivation** defines the rules that turn that revision into named inputs for training, validation, testing, or other declared uses.

## Workflow

1. Inspect the project, then invoke `grill-with-docs`. Stop if unavailable. Align the Dataset revision, named inputs, derivation rules, boundaries, and implementation constraints.
2. Reuse a matching `REVISION.md`; create one only for a new Dataset directory. If the required revision record is absent, incomplete, or incompatible, stop and identify what the user must resolve. Create or update the selected `DERIVATION.md`.
3. Implement the derivation under `data/<dataset-id>/derivations/<derivation-id>/`. Keep its contract, code, and configuration consistent and self-contained. If implementation requires changing the aligned revision, inputs, rules, or boundaries, rerun `grill-with-docs`.
4. Record the explicit Python file or directory scope changed. Run `improve-python-documentation`, then `fix-python-quality`, with that same scope.
5. Report what you've done. Do not commit.

## Layout

```text
data/<dataset-id>/
  REVISION.md
  derivations/<derivation-id>/
    DERIVATION.md
    configs/<preset>.yaml  # optional
    <implementation files>
```

A Dataset directory contains one raw revision shared by its derivations. Keep each derivation's processing code and configuration within its directory, even when this duplicates another derivation; do not introduce shared processing directories such as `common/`, `lib/`, or `_shared/`.

Do not create `dataset.yaml` or `derivation.yaml`.

## Contracts

### REVISION.md

Document the raw revision shared by all derivations with these sections:

- **Description**: identity, purpose, boundaries, source, version, acquisition, content identity, license, and access conditions.
- **Raw Data**: files, fields, sample structure, labels, and availability.
- **Known Constraints**: missing content, data quality, source limitations, and access or licensing restrictions.

### DERIVATION.md

Document one derivation without copying complete configuration or source code:

- **Description**: purpose and boundaries.
- **Dataset Derivation**: referenced `REVISION.md`, raw data used, and how it is transformed into named inputs.
- **Derived Data**: purpose, selection boundary, format, fields, and label semantics for each named input. Prefer to use `train`, `validation`, and `test` for standard splits, but allow other names when appropriate.
- **Implementation**: files, configurations, dependencies, generation commands, materialized or cached artifacts.
