---
name: define-dataset
description: Define a self-contained, reusable Dataset component, including its source revision and the rules that derive usable inputs.
---

# Define Dataset

Treat each **Dataset** as an independent, reusable component. A **Dataset revision** identifies the source content; a **Dataset derivation** defines the rules that turn that revision into named inputs.

## Workflow

### Dataset revision

1. Skip this stage if `REVISION.md` already exists.
2. Thoroughly inspect the user-provided raw dataset, including all data and annotations, and summarize your findings without omissions.
3. Record the findings in `REVISION.md`.

### Dataset derivation

1. Inspect the project, then invoke `grill-with-docs`. Align the Dataset revision, named inputs, derivation rules, boundaries, and implementation constraints.
2. Create or update the selected `DERIVATION.md`.
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

- **Description**: identity, boundaries, source, version, acquisition, content identity, license, and access conditions.
- **Raw Data**: files, fields, sample structure, labels, and availability.
- **Known Constraints**: missing content, data quality, source limitations, and access or licensing restrictions.

### DERIVATION.md

Document one derivation without copying complete configuration or source code:

- **Description**: identity and boundaries.
- **Dataset Derivation**: referenced `REVISION.md`, raw data used, and its transformation into named inputs.
- **Derived Data**: selection boundary, format, fields, and label semantics for each named input. Prefer `train`, `validation`, and `test`; allow other split names when needed.
- **Implementation**: files, configurations, dependencies, generation commands, and materialized or cached artifacts.
