---
name: train-experiment
description: Execute or document the training phase of a fixed deep-learning Experiment revision and record its checkpoint and phase provenance.
---

# Train Experiment

This entry owns the `train` phase only. It can be called directly for a standalone training phase or delegated to by `$run-experiment`. It does not evaluate a checkpoint, compare metrics across runs, or write the final report bundle.

Unless the Experiment spec declares another location, write training manifests and artifact references under `results/<experiment-id>/<result-id>/`.

## Inputs

Read the fixed Experiment revision, the Benchmark's selected Dataset derivation inputs, Model and Benchmark component revisions, optional training settings, dependency identity, resource assignment, and execution context. Verify that the declared training inputs are available and that the output checkpoint path or locator is writable.

Before direct execution, require the Experiment control commit and the Git preflight described by `$run-experiment`: consistent Experiment files, clean source paths, complete component SHAs, existing commits, and current checkout paths matching each component commit. When delegated, consume the orchestrator's verified context rather than repeating or weakening it.

When called by `$run-experiment`, read and follow the supplied `EXECUTION.md`, then use the parent run ID, assigned devices, cancellation state, working directory, and execution context supplied by the orchestrator. Do not allocate a second set of GPUs or silently change the request.

For a direct invocation, apply `$run-experiment`'s execution-specification workflow before starting. `$grill-with-docs` is required; if it is unavailable, stop. Write `results/<experiment-id>/<result-id>/EXECUTION.md` with the bound Experiment revision, commands and resources, output locations, cancellation and failure handling, and verification. If training requires a normative change to that specification, pause and repeat `$grill-with-docs` before continuing.

## Training procedure

1. Record the exact training command, Experiment training settings, seed, Benchmark input mapping, component revisions, assigned physical GPU indices, and output locations.
2. Run the target project's own training launcher and environment. The skill may provide PyTorch code or command snippets in Markdown, but it does not install a generic runner.
3. Preserve stdout/stderr, checkpoints, training curves, and intermediate artifacts by stable path or locator and digest where practical.
4. Verify that the declared checkpoint exists and can be identified by the model revision, configuration, and training run that produced it.
5. Write the phase manifest and update the shared execution context before returning control to the orchestrator.

## Phase manifest

```yaml
experiment_id: <experiment>
run_id: <run>
phase: train
status: completed | failed | cancelled | partial
started_at: <timestamp>
finished_at: <timestamp>
command: <exact command or immutable command record>
component_commits:
  model: {repository: <id>, commit: <40-character SHA>, paths: [<paths>]}
  dataset_derivations:
    - id: <dataset-derivation-id>
      repository: <id>
      commit: <40-character SHA>
      paths: [<paths>]
  benchmark: {repository: <id>, commit: <40-character SHA>, paths: [<paths>]}
  dependencies: {repository: <id>, commit: <40-character SHA>, paths: [<paths>]}
experiment_spec_commit: <40-character SHA>
benchmark_inputs: [<named Benchmark inputs>]
seed: <integer>
assigned_gpus: [<physical indices>]
checkpoint: {path_or_locator: <value>, digest: <value>, model_revision: <value>}
metrics: [<training metric record locators>]
log: <path or locator>
reason: <required for failed, cancelled, or partial status>
```

Return the manifest and artifact locators. A failed or partial training phase is not a successful experiment; `$run-experiment` decides whether an evaluation phase may continue under `allow_partial_train`. The manifest belongs under the parent Result directory.
