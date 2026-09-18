---
name: run-experiment
description: Execute a confirmed experiment on a researcher-controlled multi-GPU machine with phase-level provenance and no cloud runtime assumption.
---

# Run Experiment

Run only a `confirmed` Experiment spec. Before starting, verify that code, Dataset setup, configuration, training/evaluation commands, metric, and dependencies are fixed revisions. A draft or dirty run must be labeled exploratory and cannot silently produce formal evidence.

## Resource plan

Show the requested GPU count, allowed indices, minimum free memory, queue timeout, working directory, commands, output paths, and cancellation method. Observe local NVIDIA devices and choose only devices satisfying the request. Coordinate concurrent runs through a shared machine-level lock convention; lock records contain GPU index, process owner, and acquisition time. Reclaim locks only when ownership is demonstrably stale, and release only your own locks.

Do not add cloud allocation or silently change a request. If no local assignment satisfies the request, preserve a queued or blocked state and ask how to proceed.

## Phase execution

Treat `train` and `evaluate` independently. Before evaluation, verify that the declared checkpoint exists and that its compatibility and Dataset input match the spec. Evaluation may consume an explicitly permitted partial checkpoint; mark the training phase and final run as partial. A failed or cancelled train phase blocks evaluation unless the spec explicitly permits partial work.

Use the target project's own launcher and environment. Set `CUDA_VISIBLE_DEVICES` or the project's equivalent only after recording the assigned physical indices. Do not introduce a generic Python runner.

## Required records

Create or update a machine-readable execution context before the first phase and after every phase:

```yaml
id: execution-context-<run-id>
captured_at: <timestamp>
git_commit: <commit or null>
python: <runtime identity>
pytorch: <runtime identity>
cuda: <runtime/driver identity or null>
dependencies: {lockfile: <path>, digest: <digest>}
hardware: {machine: <identity>, gpu_inventory: [<observations>]}
assigned_devices: {train: [<indices>], evaluate: [<indices>]}
resource_observations: {<phase>: <requested and observed resources>}
phase_statuses: {train: completed | failed | cancelled | blocked, evaluate: <status>}
```

For each phase, write a manifest with experiment ID, phase, start/end timestamps, command identity, component revisions, Dataset setup/input, checkpoint, assigned GPUs, log/artifact locators, return status, and a reason for any failure or block. Write a parent manifest that reports `completed` only when every declared phase completed; otherwise report `partial` and retain all phase states.
