---
name: run-experiment
description: Run the phases declared by a fixed Experiment.
---

# Run Experiment

`run-experiment` runs one fixed Experiment. The Experiment defines its environment, phases, resources, commands, failure rules, and outputs.

## Workflow

1. Read the fixed Experiment and the target project's instructions.
2. Confirm the Experiment control commit. If it is missing, show the exact diff, paths, and commit message and create it only after explicit user confirmation.
3. Prepare the declared environment and run the declared phases in order.
4. Check that the declared outputs exist and report the result. Propose the files and message for a user-created Result commit.

Return to `$define-experiment` when the Experiment contract must change. Report failed phases or missing outputs; do not create the Result commit.
