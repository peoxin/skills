---
name: run-experiment
description: Execute one fixed deep-learning Experiment on a researcher-controlled multi-GPU machine and record its phases, provenance, outputs, and report data.
---

# Run Experiment

Execute the phases declared by one fixed Experiment revision. This is the only execution entry: it owns resource allocation, training, evaluation, provenance, cancellation, and the parent Result records. The Experiment definition owns the research question, selected components, commands, configuration, resources, checkpoint flow, outputs, and failure boundaries.

Do not accept temporary phase, command, configuration, resource, or output overrides. If the requested run needs a normative change, stop and hand the change to `$define-experiment`.

Unless the Experiment declares another location, use:

```text
results/<experiment-id>/<result-id>/
```

Keep the execution context, parent Result manifest, logs, checkpoints, metrics, reports, figures, and artifact references under that Result directory. Do not create `EXECUTION.md` or separate train/evaluate phase manifest files.

## Workflow

1. Read the target project's instructions, the fixed Experiment directory, its `EXPERIMENT.md`, configuration or phase implementation, selected component paths, and dependency conventions.
2. Determine the declared phases from the Experiment. Execute only `Train`, `Evaluate`, or both in the declared order. A missing phase is not an implicit success or failure.
3. Run the formal preflight. Verify the Experiment control commit, complete component bindings, clean selected source paths, existing commits, and checkout paths matching every recorded component revision. If these checks fail, stop before a formal run.
4. If required component or Experiment commits are missing, optionally prepare them only after the user explicitly chooses automatic preparation. Show every diff, file scope, proposed commit message, and the Experiment control commit's component bindings. Ask for explicit confirmation covering that exact submission set. Create only the confirmed commits; a later or different submission set requires a new confirmation.
5. If the user explicitly permits an exploratory run despite incomplete formal provenance, label the Result exploratory and record every missing or dirty input. Never present it as formal evidence.
6. Create the Result directory and write the initial execution context before the first phase. Record the Experiment revision, component bindings, runtime identity, hardware observations, requested resources, and initial statuses.
7. Observe local NVIDIA devices and allocate only devices satisfying the Experiment request. Record physical indices, free memory, queue or reservation state, working directory, and cancellation method. Use the target project's own launcher and environment; do not add a generic runner or scheduler.
8. Execute each declared phase using its Experiment command, configuration, inputs, assigned devices, and output locations. Preserve stdout, stderr, checkpoints, metrics, reports, figures, and intermediate artifacts by stable path or locator and digest where practical.
9. Before each phase and after it returns, repeat HEAD, source-path, Experiment configuration, and component-path verification. A mismatch blocks the next phase and is recorded as a provenance failure.
10. Update the execution context and parent Result manifest after every phase. Retain each phase status, exact command, resources, outputs, failure reason, and provenance observations. For Evaluate, follow `evaluation-report.md`.
11. Apply the Experiment's continuation rules. A failed, cancelled, or partial Train blocks Evaluate unless the Experiment explicitly permits partial training. An Evaluate-only Experiment uses its declared external or prior-Result checkpoint and never invents a Train phase.
12. After all declared phases, verify that the Experiment, execution context, parent manifest, outputs, and observed behavior agree. List every Result file and propose a Result commit message. Ask the user to create the Result commit; do not create that Result commit automatically.

A Result is `completed` only when every declared phase completed and all post-run checks pass. Otherwise report the concrete phase statuses and mark the Result `partial`, `failed`, `cancelled`, or `blocked` as applicable.

## Automatic component preparation

Automatic preparation is a user-authorized convenience, not a default. It may create separate commits for changed Model, Dataset derivation, Benchmark, or dependency components and then an Experiment control commit. It must:

- show the diff and exact paths for every proposed component commit;
- keep each reusable component commit separate from the Experiment definition and unrelated work;
- show the proposed Experiment commit and its complete component bindings;
- obtain an explicit user confirmation for that exact set of commits immediately before writing any commit;
- stop without modifying or committing anything when confirmation is absent or refused;
- ask again if the file set, component revisions, or commit messages change.

The user may also provide already-fixed component revisions and an Experiment control commit. Reuse them when their declared paths match.

## Formal preflight

A formal run requires all of the following:

1. The complete Experiment directory is committed, and `EXPERIMENT.md` agrees with every declared configuration file and phase implementation. Record its full SHA as `experiment_spec_commit`.
2. The Experiment control commit message lists every selected Model, Dataset derivation, Benchmark, and dependency with its repository, complete 40-character SHA, and paths. Verify every commit exists.
3. Every declared source checkout is clean for its selected paths and has a successful `git rev-parse HEAD`.
4. Every selected path matches its recorded component commit using `git diff --quiet <commit> -- <path>` or an equivalent immutable comparison.
5. The declared commands, resources, inputs, checkpoint flow, output locations, and failure rules are available without adding execution-time behavior.

A dirty or unbound input may be used only for an explicitly authorized exploratory Result. Record the reason and missing provenance instead of silently upgrading it to formal evidence.

## Resource allocation and cancellation

Show the requested GPU count, allowed indices, minimum free memory, queue timeout, working directory, commands, output paths, and cancellation method before allocation. Coordinate concurrent runs through a shared machine-level lock convention; lock records contain GPU index, process owner, and acquisition time. Reclaim locks only when ownership is demonstrably stale, and release only your own locks.

Do not add cloud allocation or silently change a request. If no local assignment satisfies it, preserve a queued or blocked state and ask how to proceed. Record cancellation, partial-work policy, cleanup, and blocking failures in the parent Result manifest from the Experiment's declared rules.

## Execution context

Write or update this machine-readable context before the first phase and after every phase:

```yaml
id: execution-context-<run-id>
captured_at: <timestamp>
experiment_spec_commit: <40-character SHA>
exploratory: false
component_commits:
  model: {repository: <id>, commit: <40-character SHA>, paths: [<paths>]}
  dataset_derivations:
    - id: <dataset-derivation-id>
      repository: <id>
      commit: <40-character SHA>
      paths: [<paths>]
  benchmark: {repository: <id>, commit: <40-character SHA>, paths: [<paths>]}
  dependencies: {repository: <id>, commit: <40-character SHA>, paths: [<lockfile paths>]}
git: {observed_head: <full SHA or null>, source_paths_clean: true}
python: <runtime identity>
pytorch: <runtime identity>
cuda: <runtime/driver identity or null>
dependencies: {lockfile: <path>, digest: <digest>}
hardware: {machine: <identity>, gpu_inventory: [<observations>]}
assigned_devices: {train: [<indices>], evaluate: [<indices>]}
resource_observations: {<phase>: <requested and observed resources>}
phase_statuses: {train: <status>, evaluate: <status>}
missing_provenance: []
```

## Parent Result manifest

Write one parent manifest under the Result directory. It is authoritative for execution status and links to phase outputs without duplicating metric or report prose:

```yaml
experiment_id: <experiment>
run_id: <run>
status: completed | partial | failed | cancelled | blocked
started_at: <timestamp>
finished_at: <timestamp>
experiment_spec_commit: <40-character SHA>
execution_context: <path>
phases:
  - name: train | evaluate
    status: completed | failed | cancelled | partial | blocked
    command: <exact command or immutable command record>
    assigned_gpus: [<physical indices>]
    outputs: [<path or locator>]
    manifest_data: <phase-specific structured record path or inline fields>
    reason: <required when not completed>
checkpoint: {path_or_locator: <value>, digest: <value>, model_revision: <value>}
metrics: [<training metric record locators>]
evaluation_report: <report bundle path or null>
logs: [<path or locator>]
missing_provenance: []
```

The parent manifest may link to phase-specific structured records when the target project needs them, but the user-facing execution entry remains this skill and the Result has one authoritative parent status. Do not copy evaluation metric definitions or report prose into the parent manifest.

After execution, verify the complete Result directory and propose a user-created Result commit. A formal Result is not Git-tracked until that commit exists.
