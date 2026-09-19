---
name: run-experiment
description: Orchestrate a fixed deep-learning Experiment revision on a researcher-controlled multi-GPU machine with an aligned execution specification and shared provenance.
---

# Run Experiment

This is the orchestration entry. For formal work, run only an Experiment control commit containing consistent `EXPERIMENT.md` and its declared configuration or phase implementation. Before starting, verify that the Model, every Dataset derivation selected by the Experiment or Benchmark, the self-contained Benchmark and metric implementation, and dependencies are fixed revisions. Read the component repository, commit, and paths from the Experiment control commit message. Uncommitted or dirty inputs make the run exploratory and cannot silently produce formal evidence.

Delegate phase work instead of reimplementing it:

- `train` phase: call `$train-experiment`.
- `evaluate` phase: call `$evaluate-experiment`.

Both child skills remain independently callable. The orchestrator passes them the parent run ID, phase inputs, execution context, assigned devices, cancellation state, and output locations.

Unless the Experiment revision declares another location, use `results/<experiment-id>/<result-id>/` for the Result directory. Keep `EXECUTION.md`, manifests, report data, rendered reports, figures, and artifact references under that Result directory.

## Align the execution specification

Before creating or normatively changing a Result execution, invoke `$grill-with-docs`. If it is unavailable, stop. Work the design tree until its frontier is empty, present the complete shared understanding, and wait for explicit user confirmation before writing `EXECUTION.md` or starting a phase.

Keep `EXECUTION.md` brief and use these sections:

- **Bound experiment revision**: the Experiment control commit and the fixed component revisions it references.
- **Commands and resources**: phase commands, working directory, GPU constraints and assignments, timeouts, and seed.
- **Output locations**: manifests, checkpoints, metrics, logs, reports, figures, and external artifact locators.
- **Cancellation and failure handling**: cancellation method, partial-work policy, cleanup, and blocking failures.
- **Verification**: preflight, phase-boundary, output, and provenance checks.

`EXECUTION.md` is the human-readable execution contract. The execution context and manifests hold exact machine-consumed fields and must remain consistent with it without copying their complete contents into Markdown. If preparation or execution requires a normative change to phases, resources, commands, outputs, verification, or failure handling, pause and repeat `$grill-with-docs`; formatting and mechanical changes that preserve the specification do not require another interview.

## Fix Component Revisions

Before formal preflight, resolve the component bindings in the Experiment control commit message. Reuse an existing fixed commit when its declared paths match the selected component. If a selected reusable component needs a new revision and the user has chosen automatic preparation, show its diff and commit that component alone. Prepare changed Model, Dataset derivation, Benchmark, and dependency components separately; never combine them with the Experiment definition or unrelated work. Then commit the Experiment directory as the Experiment control commit with the component bindings in its commit message.

When automatic preparation is not selected, stop with the component diffs, required commits, and proposed Experiment commit message for the user. Do not treat uncommitted component paths as formal inputs.

## Git preflight

Formal execution is refused until all of these checks pass:

1. The complete Experiment directory is committed and its `EXPERIMENT.md` agrees with every declared configuration file and phase implementation. Record the full SHA as `experiment_spec_commit` in the run manifest. Read the component bindings from the Experiment control commit message. Read the Benchmark at its declared component commit and require its `BENCHMARK.md` and implementations to agree; a missing or mismatched component blocks formal execution.
2. `git status --porcelain=v1` is empty for source paths in every declared repository and `git rev-parse HEAD` succeeds. Record each observed HEAD in the execution context. The Experiment control commit message is the version binding for the formal run. It must list every selected Model, Dataset derivation, Benchmark, and dependency source with its repository, complete commit SHA, and paths. Verify each listed commit exists.
3. For every component binding read from the Experiment control commit message, compare the relevant checkout with the recorded commit (`git diff --quiet <commit> -- <path>` or an equivalent `git show <commit>:<path>` comparison). A mismatch, missing path, short SHA, branch, tag, or uncommitted source change blocks the run.
4. Before each phase and after each child skill returns, repeat the HEAD, source-path, Experiment configuration, and component-path checks. A change blocks the next phase and is recorded as a provenance failure.

Write `EXECUTION.md` before phase execution and keep it consistent with the machine-readable records written during the run. The user reviews and commits the complete Result directory as a Result commit after the run. The orchestrator must never create that commit implicitly.

## Resource allocation

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
git_commit: <observed HEAD full SHA or null for exploratory work>
experiment_spec_commit: <40-character SHA>
component_commits:
  model: {repository: <id>, commit: <40-character SHA>, paths: [<paths>]}
  dataset_derivations:
    - id: <dataset-derivation-id>
      repository: <id>
      commit: <40-character SHA>
      paths: [<paths>]
  benchmark: {repository: <id>, commit: <40-character SHA>, paths: [<paths>]}
  dependencies: {repository: <id>, commit: <40-character SHA>, paths: [<lockfile paths>]}
python: <runtime identity>
pytorch: <runtime identity>
cuda: <runtime/driver identity or null>
dependencies: {lockfile: <path>, digest: <digest>}
hardware: {machine: <identity>, gpu_inventory: [<observations>]}
assigned_devices: {train: [<indices>], evaluate: [<indices>]}
resource_observations: {<phase>: <requested and observed resources>}
phase_statuses: {train: completed | failed | cancelled | blocked, evaluate: <status>}
```

Persist the execution context before delegating the first phase and after every child returns. Child skills write phase manifests; the orchestrator verifies their parent/run IDs, repeats Git component verification, appends orchestration status and resource observations, and writes the parent manifest. The parent manifest reports `completed` only when every declared phase completed; otherwise report `partial` and retain all phase states. Do not duplicate child metrics or report prose in the parent manifest; link to the child manifest and `$evaluate-experiment` report bundle instead.

After the run, verify that `EXECUTION.md`, the execution context, phase manifests, outputs, and observed behavior agree. List the complete Result files and a proposed commit message. Ask the user to create the Result commit, then record or report its full SHA. A formal result is not fully Git-tracked until that commit exists.
