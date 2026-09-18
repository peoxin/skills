---
name: dataset-setup
description: Register a raw dataset revision and define a reproducible Dataset setup with explicit inputs, splits, and processing.
---

# Dataset Setup

Keep the source dataset and its use separate. A raw Dataset revision is not directly consumable by training or evaluation. A Dataset setup derives named usable inputs from that revision.

## Default directory

Use the target project's root-level `data/` directory. Keep one logical Dataset per directory and put each usable setup below it:

```text
data/
  <dataset-id>/
    dataset.yaml
    setups/
      <setup-id>/
        setup.yaml
        prepare.py
        README.md
```

The directory names are logical identities, not versions. A setup directory may contain its processing code, configuration, and derived-input records. Raw and processed data files may live there or at an explicitly recorded external path, but large files are not required to be committed to Git.

## Dataset revision

Record provenance, content identity, access conditions, and retention:

```yaml
id: dataset-revision-<stable-id>
source: <URL, local locator, or registry identifier>
digest: <content or release digest>
license: <license and access conditions>
retention: <what is retained and for how long>
version: <source version or release>
```

## Dataset setup

Record every rule that can change examples or labels:

```yaml
id: dataset-setup-<stable-id>
dataset_revision: dataset-revision-<id>
setup_revision:
  repository: <Git remote or repository identifier>
  commit: <40-character SHA>
  paths: [<data/<dataset-id>/setups/<setup-id> paths>]
inputs:
  - name: train
    source_selection: <selection or generation rule>
    preprocessing: [<ordered transformations>]
    sampling: <sampling rule and seed>
    label_mapping: <mapping revision or rule>
    digest: <derived input digest or null>
  - name: validation
    source_selection: <rule>
  - name: test
    source_selection: <rule>
  - name: custom-example-set
    source_selection: <rule>
unavailable_inputs: [<input name and reason>]
notes: <differences from the source dataset definition>
```

Use `train`, `val`, and `test` when present. Evaluation selects one of the declared inputs. Support custom splits, filtering, sampling, preprocessing, augmentation, and label semantics without modifying the raw Dataset revision.

Check leakage, class/label transformations, random seeds, derived digests, and whether the declared files actually exist. Ask before saving a formal setup. Hand the confirmed setup to `$define-experiment`.
