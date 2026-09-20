---
name: integrate-pytorch-lightning
description: Implement PyTorch Lightning within one already aligned Model or Experiment contract.
---

# Integrate PyTorch Lightning

Implement Lightning within one already aligned component contract.

## Scope

Work in exactly one aligned component. Default to Experiment scope. Use Model scope only when `MODEL.md` explicitly requires a framework-native Lightning model.

If the component contract is missing, does not select Lightning, or must change, return to `define-model` or `define-experiment`. Ask the user only when the aligned contract does not determine the component scope or required behavior.

## Implement

### Model

Follow `MODEL.md` and implement the framework-native Lightning model inside the selected Model directory without changing the aligned contract.

### Experiment

Follow `EXPERIMENT.md` and implement the Lightning integration inside the selected Experiment directory using its selected Model and Dataset interfaces, without changing the aligned contract.
