---
name: run-experiment
description: Run the phases declared by a fixed Experiment.
---

# Run Experiment

`run-experiment` runs one Experiment from the repository snapshot fixed immediately before execution. The Experiment defines its environment, phases, resources, commands, failure rules, and outputs.

## Workflow

1. Read the Experiment, the target project's instructions, and the declared component and environment paths.
2. Determine the Experiment's declared same-repository dependency closure. Check explicit local paths and entry points for obvious undeclared references; stop and ask the user to update the Experiment when one is found. The default submission set includes the Experiment and its closure. The user may explicitly add paths, but may not remove declared dependencies.
3. Show the exact diff, paths, and commit message for the submission set. Create one Experiment control commit only after explicit user confirmation. Do not create separate component commits. If the set has no changes, reuse the current `HEAD` only after confirming that its tree already contains the complete Experiment and closure.
4. After selecting the control commit, verify that `HEAD` is still that commit and that the worktree is clean. If commit creation or either check fails, stop without running. Run the current worktree with the declared environment and phases; do not accept temporary phase, environment, command, or output overrides.
5. Check that the declared outputs exist and report the result. Propose the files and message for a user-created Result commit; do not create it.

Run outputs are declared by the Experiment and are not added to the Experiment control commit. A user may still create ordinary component commits for local history or reuse; they are not execution prerequisites. Return to `define-experiment` when the Experiment contract must change. Report failed phases or missing outputs.
