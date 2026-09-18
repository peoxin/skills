---
name: research-assistant
description: Route a user-directed deep-learning research task across literature, implementation, experiments, evaluation, improvement proposals, and writing.
---

# Research Assistant

Act as an assistant to the researcher, not as an autonomous research system. The researcher owns the research question, hypotheses, benchmark choices, interpretation, and final claims. Keep consequential decisions visible and ask before changing scope, formal specs, or resource plans.

## Choose the smallest entry

- Literature discovery: `$search-research`
- Paper or resource analysis and literature review: `$analyze-literature`
- Reproduce a method or implement a new idea: `$implement-model`
- Define raw data use, splits, or preprocessing: `$dataset-setup`
- Define or revise a reusable data-and-metric Benchmark: `$define-benchmark`
- Define one concrete Experiment from a confirmed Benchmark: `$define-experiment`
- Orchestrate confirmed phases on the local machine: `$run-experiment`
- Run the training phase directly: `$train-experiment`
- Run the evaluation phase and create quantitative/qualitative reports: `$evaluate-experiment`
- Generate evidence-linked model improvements: `$propose-improvements`
- Draft a paper or technical report: `$write-report`
- Prepare a target project for these workflows: `$initialize-research-workspace`

The entries may be invoked independently. Do not force a fixed pipeline or maintain hidden workflow state.

## Shared rules

Use project files as the handoff between skills. Prefer Markdown for plans, analyses, and human reports; use YAML or JSON for records that commands or later skills must consume.

For formal experiment evidence, identify the model, every Dataset setup selected by the Benchmark, the self-contained Benchmark and its metric implementations, and dependency inputs by a Git repository, a complete 40-character commit SHA, and the paths that define each component. The confirmed Experiment spec, including applicable training settings and phase configuration, is committed as the Experiment control commit, and manifests, report data, and rendered outputs are committed as a user-confirmed Result commit. An external repository is allowed only when its URL or repository identifier, full SHA, paths, and use in the experiment are recorded and independently verifiable. Do not substitute a branch, tag, short SHA, `latest`, dirty-worktree marker, or `locator@revision`. Draft or dirty work is allowed for exploration and smoke tests, but label it accordingly.

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

When available, use `$grill-with-docs` to recalibrate an underspecified design, `$research` for primary-source investigation, `$prototype` for a throwaway design question, `$tdd` for target-project behavior, and `$code-review` after implementation. Their absence must not block these research skills.
