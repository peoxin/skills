---
name: run-experiment
description: Orchestrate confirmed deep-learning experiment phases on a researcher-controlled multi-GPU machine with shared provenance and no cloud runtime assumption.
---

# Run Experiment

This is the orchestration entry. Run only a `confirmed` Experiment spec. Before starting, verify that the model, every Dataset setup selected by the Benchmark, self-contained Benchmark and metric implementation, Experiment configuration, and dependencies are fixed revisions. A draft or dirty run must be labeled exploratory and cannot silently produce formal evidence.

Delegate phase work instead of reimplementing it:

- `train` phase: call `$train-experiment`.
- `evaluate` phase: call `$evaluate-experiment`.

Both child skills remain independently callable. The orchestrator passes them the parent run ID, phase inputs, execution context, assigned devices, cancellation state, and output locations.

Unless the confirmed Experiment spec declares another location, use `results/<experiment-id>/<result-id>/` for the Result directory. Keep manifests, report data, rendered reports, figures, and artifact references under that Result directory.

## Git preflight

Formal execution is refused until all of these checks pass:

1. The confirmed Experiment spec is itself committed. Record the full SHA as `experiment_spec_commit` in the run manifest.
2. `git status --porcelain=v1` is empty for source paths in every declared repository and `git rev-parse HEAD` succeeds. Record each observed HEAD in the execution context.
3. Every declared component revision has a repository identifier, a complete 40-character commit SHA, and one or more paths. Verify the commit exists with `git cat-file -e <commit>^{commit}`.
4. For every declared path, compare the relevant checkout with the recorded commit (`git diff --quiet <commit> -- <path>` or an equivalent `git show <commit>:<path>` comparison). A mismatch, missing path, short SHA, branch, tag, or uncommitted source change blocks the run.
5. Before each phase and after each child skill returns, repeat the HEAD, source-path, and component-path checks. A change blocks the next phase and is recorded as a provenance failure.

Result files may be written to the declared result directory after phase execution. They are not source revisions: the user reviews and commits them as a Result commit after the run. The orchestrator must never create that commit implicitly.

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
git_commit: <observed HEAD full SHA or null for exploratory work>
experiment_spec_commit: <40-character SHA>
component_commits:
  model: {repository: <id>, commit: <40-character SHA>, paths: [<paths>]}
  dataset_setups:
    - id: <dataset-setup-id>
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

After the run, list the canonical result files and a proposed commit message. Ask the user to create the Result commit, then record or report its full SHA. A formal result is not fully Git-tracked until that commit exists.
