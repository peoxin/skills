---
name: run-experiment
description: Orchestrate confirmed deep-learning experiment phases on a researcher-controlled multi-GPU machine with shared provenance and no cloud runtime assumption.
---

# Run Experiment

This is the orchestration entry. Run only a `confirmed` Experiment spec. Before starting, verify that code, Dataset setup, configuration, training/evaluation commands, metric, and dependencies are fixed revisions. A draft or dirty run must be labeled exploratory and cannot silently produce formal evidence.

Delegate phase work instead of reimplementing it:

- `train` phase: call `$train-experiment`.
- `evaluate` phase: call `$evaluate-experiment`.

Both child skills remain independently callable. The orchestrator passes them the parent run ID, phase inputs, execution context, assigned devices, cancellation state, and output locations.

## Resource plan

Show the requested GPU count, allowed indices, minimum free memory, queue timeout, working directory, commands, output paths, and cancellation method. Observe local NVIDIA devices and choose only devices satisfying the request. Coordinate concurrent runs through a shared machine-level lock convention; lock records contain GPU index, process owner, and acquisition time. Reclaim locks only when ownership is demonstrably stale, and release only your own locks.

Do not add cloud allocation or silently change a request. If no local assignment satisfies the request, preserve a queued or blocked state and ask how to proceed.

## Phase orchestration

Read only the phases declared in the Experiment spec:

- train only: allocate resources and delegate to `$train-experiment`;
- evaluate only: allocate resources and delegate to `$evaluate-experiment` using the declared or external checkpoint;
- train followed by evaluate: wait for the training manifest, then check checkpoint and `allow_partial_train` before delegating evaluation.

A failed or cancelled train phase blocks evaluation unless the spec explicitly permits partial work. An evaluation-only experiment must not invent a train phase.

Allocate or reserve physical GPUs once per phase, record the assignment, and pass it to the child skill. Use the target project's own launcher and environment. Set `CUDA_VISIBLE_DEVICES` or the project's equivalent only after recording the assigned physical indices. Do not introduce a generic Python runner or a second scheduler.

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

Persist the execution context before delegating the first phase and after every child returns. Child skills write phase manifests; the orchestrator verifies their parent/run IDs, appends orchestration status and resource observations, and writes the parent manifest. The parent manifest reports `completed` only when every declared phase completed; otherwise report `partial` and retain all phase states. Do not duplicate child metrics or report prose in the parent manifest; link to the child manifest and `$evaluate-experiment` report bundle instead.
