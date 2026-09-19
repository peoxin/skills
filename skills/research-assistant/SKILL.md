---
name: research-assistant
description: Route a user-directed deep-learning research task across literature, implementation, experiments, evaluation, improvement proposals, and writing.
---

# Research Assistant

Act as an assistant to the researcher, not as an autonomous research system. The researcher owns the research question, hypotheses, benchmark choices, interpretation, and final claims. Keep consequential decisions visible and ask before changing scope, component specifications, or resource requirements.

## Choose the smallest entry

- Literature discovery: `$search-research`
- Paper or resource analysis and literature review: `$analyze-literature`
- Reproduce a method or implement a new idea: `$implement-model`
- Define raw data use, splits, or preprocessing: `$dataset-setup`
- Define or revise a reusable data-and-metric Benchmark: `$define-benchmark`
- Define one concrete Experiment from a committed Benchmark revision: `$define-experiment`
- Orchestrate a fixed Experiment revision on the local machine: `$run-experiment`
- Run the training phase directly: `$train-experiment`
- Run the evaluation phase and create quantitative/qualitative reports: `$evaluate-experiment`
- Generate evidence-linked model improvements: `$propose-improvements`
- Draft a paper or technical report: `$write-report`
- Prepare a target project for these workflows: `$initialize-research-workspace`

The entries may be invoked independently. Do not force a fixed pipeline or maintain hidden workflow state.

## Shared rules

Use project files as the handoff between skills. Prefer Markdown for component specifications, analyses, and human reports; use YAML or JSON for exact records that commands or later skills must consume.

For formal experiment evidence, identify the model, every Dataset setup selected by the Benchmark, the self-contained Benchmark and its metric implementations, and dependency inputs by a Git repository, a complete 40-character commit SHA, and the paths that define each component. The Experiment control commit contains the component specification and machine-readable experiment record, including applicable training settings and phase configuration. Manifests, report data, rendered outputs, and the execution specification are committed as a user-created Result commit. An external repository is allowed only when its URL or repository identifier, full SHA, paths, and use in the experiment are recorded and independently verifiable. Do not substitute a branch, tag, short SHA, `latest`, dirty-worktree marker, or `locator@revision`. Uncommitted work is allowed for exploration and smoke tests, but label it accordingly.

## Component specification workflow

Before creating or normatively changing a Dataset setup, Model, Benchmark, Experiment, or Result execution, invoke `$grill-with-docs`. It is a hard dependency for these planning stages: if it is unavailable, stop before writing the specification or implementing the change.

Work the design tree until its frontier is empty, present the complete shared understanding, and wait for explicit user confirmation. Then write the component-specific Markdown specification before implementation:

```text
data/<dataset-id>/setups/<setup-id>/SETUP.md
models/<model-id>/MODEL.md
benchmarks/<benchmark-id>/BENCHMARK.md
experiments/<experiment-id>/EXPERIMENT.md
results/<experiment-id>/<result-id>/EXECUTION.md
```

The Markdown specification records human-readable intent, behavior, boundaries, interfaces, and verification requirements. YAML or JSON records exact machine-consumed fields; do not duplicate the complete structured record in Markdown. Keep the specification, structured records, and implementation consistent. If implementation exposes a change to behavior, interfaces, input semantics, metrics, phases, resources, verification requirements, or failure conditions, pause, invoke `$grill-with-docs` again, and update the specification only after renewed confirmation. Formatting, typo fixes, and mechanical edits that preserve the specification do not require another interview.

These files have no `draft` or `confirmed` status. After implementation and verification, show the complete component diff and a proposed commit message. The user-created commit fixes the specification, structured records, and implementation as one component revision; later normative changes require a new aligned revision.

Model one complete experiment as:

```text
Result = f_eval(
  f_train(Model, Data_train, Hyperparameter_config),
  Data_test,
  Eval_metric,
)
```

Keep these distinctions explicit:

- A raw Dataset revision identifies source content; a Dataset setup defines how it becomes usable named inputs.
- A Benchmark spec defines a reusable data-and-metric protocol that may compose multiple Dataset setups; an Experiment spec defines one concrete model/configuration/seed and its phases.
- Train and evaluate are independent phases with separate status and outputs.
- A component revision defines an intended reusable input; an execution context records what actually happened in one run. The component commit is the reproducibility binding; the observed HEAD is audit evidence.
- Machine-readable results are authoritative; Markdown reports render and interpret them without silently changing values.

## Compatible general-purpose skills

The component specification workflow requires `$grill-with-docs`. When available, use `$research` for primary-source investigation, `$prototype` for a throwaway design question, `$tdd` for target-project behavior, and `$code-review` after implementation. The absence of these other general-purpose skills must not block the research skills.
