# Deep Learning Research Assistant v1

Status: recalibrated
Audience: one researcher using Python and PyTorch on a researcher-controlled multi-GPU machine

## Purpose

This repository develops a user-directed research assistant, not an autonomous research system. Its deliverable is a portable `skills/` directory of directly callable, Markdown-defined Agent Skills for literature work, model implementation, experiment definition and execution, evaluation, improvement proposals, and technical writing.

The assistant may search, summarize, generate plans and code, run static checks, run smoke tests, queue confirmed experiments, and assemble reports. The researcher owns the research question, hypotheses, benchmark acceptance, interpretation, and final written claims.

## Target project layout

The initialized target project uses visible root-level directories organized by research component:

```text
sources/       # papers, preprints, repositories, documentation, and analyses
data/          # logical Datasets and Dataset setups
models/        # self-contained model implementations and tests
benchmarks/    # self-contained data-and-metric protocols
experiments/   # concrete Experiment specs, including optional training settings
results/       # execution results, manifests, reports, figures, and artifacts
```

The initializer creates these six root directories after confirmation. Component skills create subdirectories lazily:

```text
data/<dataset-id>/setups/<setup-id>/
models/<model-id>/
benchmarks/<benchmark-id>/metrics/
benchmarks/<benchmark-id>/visualizations/
experiments/<experiment-id>/experiment.yaml
results/<experiment-id>/<result-id>/
```

Do not create top-level `training/`, `evaluation/`, `metrics/`, `runs/`, `reports/`, `common/`, or `_shared/` directories. Training and evaluation are Experiment phases; training settings belong in `experiment.yaml`; metric implementations belong to their Benchmark; and each model is self-contained.

## Scope

In scope:

- published-paper and preprint search;
- GitHub, Hugging Face, official documentation, and high-quality resource search;
- source analysis, literature reviews, and evidence-linked notes;
- reproducing an existing model or implementing a model from a user idea through one shared model-implementation entry;
- explicit raw dataset records and reusable Dataset setups;
- reusable data-and-metric Benchmark specs and concrete Experiment specs;
- local multi-GPU execution with a queue, device assignment, and phase-level manifests;
- train and evaluate phases that may complete independently;
- external-checkpoint evaluation-only experiments;
- machine-readable records plus rendered human-readable reports with quantitative analysis and optional qualitative visualizations;
- evidence-backed model improvement proposals;
- editable paper or technical-report drafts without submission or peer-review automation;
- coexistence with Matt Pocock's general-purpose skills.

Out of scope for v1:

- cloud or provider-specific execution;
- automatic migration or refactoring of existing repositories;
- a global immutable artifact DAG, metadata service, or approval graph;
- team synchronization and multi-user governance;
- complete CV, NLP, multimodal, or other domain-specific adapter suites;
- automatic scientific conclusions, author attestations, submission, or review workflows.

## User-facing skills

Each entry is independently callable. They exchange explicit files, not hidden conversation state.

1. `initialize-research-workspace`
   Explore a target workspace, show the proposed files and conventions, obtain confirmation, then create the minimum research workspace without overwriting existing content.
2. `search-research`
   Search papers, preprints, code, model repositories, documentation, and other declared resource classes. Produce candidate Source records with source type, locator, version, access date, and verification status.
3. `analyze-literature`
   Analyze selected Source records, extract mechanisms and experimental details with locators, and assemble literature-review material.
4. `implement-model`
   Handle either `reproduce` or `new-method` intent. Produce an implementation plan, evidence or hypothesis separation, code proposal, tests, and a runnable integration plan before confirmed edits.
5. `dataset-setup`
   Register a raw Dataset revision and create or validate a Dataset setup describing splits, preprocessing, sampling, label mapping, and derived input digests.
6. `define-experiment`
   Create or revise data-and-metric Benchmark specs and concrete Experiment specs. Bind model, one or more Dataset setups, Benchmark, optional training settings, phases, seed, resources, commands, and outputs without starting formal execution.
7. `run-experiment`
   Orchestrate confirmed Experiment phases through the local multi-GPU procedure. Allocate resources, call the training and evaluation entries, preserve shared execution context, handle cancellation and partial states, and write the parent manifest.
8. `train-experiment`
   Execute or document the training phase, produce checkpoints and training metrics, and write a training phase manifest. It can be called directly or by `run-experiment`.
9. `evaluate-experiment`
   Execute or collect an evaluation phase, check checkpoint compatibility, and generate canonical metric/report records plus rendered reports, tables, plots, and optional visualization-hook outputs. It can be called directly or by `run-experiment`.
10. `propose-improvements`
   Combine Source analyses, Source collection records, and experiment reports into evidence-linked improvement proposals with hypotheses, mechanisms, risks, and ablations.
11. `write-report`
    Produce an editable paper or technical-report draft from confirmed sources, reports, and claims. It does not submit or review the document.
12. `research-assistant` (optional convenience entry)
    Recommend or sequence the skills above while preserving each skill's files, rules, and confirmation points. It is not a second workflow engine.

## Shared records

YAML or JSON is authoritative for machine-readable specs, manifests, metrics, and report data. Markdown or HTML is rendered from those records and links back to them. Direct edits to rendered reports do not change canonical results; any corrected number or provenance must be changed in the structured record and rendered again.

### Dataset and experiment layers

The data layers are intentionally separate:

```text
raw Dataset revision
  -> Dataset setup
       -> Data_train / Data_validation / Data_test or named custom inputs
```

The raw Dataset revision identifies the source content, provenance, licensing/access conditions, and retention. It is not directly consumed by training or evaluation. A Dataset setup records how usable inputs are derived, including custom splits or processing that differs from an original dataset definition. `eval` is not a default split name; evaluation is a phase that selects a declared input such as validation, test, or a custom input.

The reusable data-and-metric evaluation layer and one concrete run are different:

```text
Benchmark spec (one or more Dataset setups, inputs, metrics, aggregation)
  -> Experiment spec (one model/seed/configuration/phase instance)
  -> Result (one execution, possibly partial)
```

Benchmark does not own baselines or model comparisons. An Experiment may compare models by referencing the same Benchmark from multiple Experiment specs.

The functional model of a complete experiment is:

```text
Result = f_eval(
    f_train(Model, Data_train, Hyperparameter_config),
    Data_test,
    Eval_metric,
)
```

`Data_train` and `Data_test` are named Benchmark inputs and may be composed from multiple Dataset setups.

Every formal component is Git-addressed. A component revision records a repository, a complete 40-character commit SHA, and the paths that belong to it. A Benchmark directory is self-contained, including its metric implementations and optional visualizations; model directories are likewise self-contained and do not use a shared model directory. An execution context records the observed HEAD and all component commits used in one run; it does not replace them with a separate execution snapshot. Environment facts may change between runs; each run gets its own context record.

### Phases and partial work

An Experiment spec may contain independent `train` and `evaluate` phases. `run-experiment` orchestrates only the phases declared in the spec by calling `train-experiment` and/or `evaluate-experiment`. Training produces checkpoints and training metrics; evaluation consumes a specifically identified checkpoint and the Dataset inputs selected by the Benchmark. Training settings are part of the Experiment spec and may be absent or not applicable for evaluation-only work. Evaluation may run against an incomplete training output when the spec permits it, but the report marks the training phase as partial, failed, cancelled, or incomplete. Missing phases are never silently treated as zero or success.

An external checkpoint is registered with an External checkpoint record and evaluated through an Evaluation-only experiment. The compatibility check validates architecture, format, weights digest, input/label semantics, preprocessing, shape, dtype/device constraints, and source/license information before formal evaluation.

### Formal and exploratory work

Formal version contract:

- Model, every Dataset setup selected by the Benchmark, Benchmark (including its metric implementations), dependency lockfiles, and the confirmed Experiment spec must be identified by Git repository, complete commit SHA, and paths. The Experiment control commit fixes its training and evaluation phase settings. For an external repository, also record its URL or identifier and how the component is used; verify it from a checkout or other immutable source before execution.
- Before a formal run, each source checkout must be clean for its declared source paths. For every component, compare the checkout paths with `git show <component.commit>:<path>`; a mismatch blocks the run.
- Record the Experiment control commit containing the confirmed Experiment spec. Record the observed `git rev-parse HEAD` in the execution context for audit, but use component commits as the reproducibility binding.
- Check HEAD, source-path cleanliness, and component-path equality before each phase and after each child phase returns. A change blocks the next phase and is written to the manifest.
- After a complete or partial run, commit canonical manifests, execution context, metrics, report data, rendered reports, and figure metadata as a user-confirmed Result commit. Skills propose the files and commit message but do not commit automatically.

Draft or dirty work may support exploratory runs and smoke tests, but it is not eligible for a formal benchmark or report claim.

Formal execution requires a user-confirmed Experiment spec and resource plan. The assistant may prepare code, validate records, and run low-cost smoke tests before confirmation. It may not silently change a component, resource request, Dataset setup, or benchmark scope.

## Local execution

The first execution target is one researcher-controlled multi-GPU machine. `run-experiment` observes local device availability, accepts explicit GPU count and device constraints, explains queue or reservation steps, creates the shared execution context, delegates phase work, records assignments, supports cancellation, and releases resources after success or failure. Child phase skills do not create a second reservation when called by the orchestrator. There is no cloud implementation or bundled scheduler in v1.

Large checkpoints, datasets, logs, and image collections need not be committed to Git. Their manifests record path or external reference, size, SHA256, generation/download time, and the component commits that produced or consumed them. Dataset processing code, Dataset setup records, checkpoint registration records, specs, code, configs, reports, and small provenance records are Git-tracked.

## Literature and knowledge

`search-research` creates candidate Source records. `analyze-literature` consumes selected records and creates locator-bound Source analyses. A literature review aggregates selected analyses; search snippets and unverified summaries are never silently promoted to facts. The local Source collection is file-based and is updated only after explicit user request or confirmation.

## Evidence and writing

Numbers, comparisons, data-processing descriptions, and literature facts in reports link to canonical source or experiment records. Improvement proposals distinguish reported evidence, inference, and new hypothesis. `write-report` can add discussion and narrative, but authoritative metrics and provenance remain in structured records. The output is a draft paper or technical report, not a submission package.

## Initialization and Matt compatibility

`initialize-research-workspace` follows the setup-matt-pocock-skills interaction pattern:

1. explore the target directory and existing `AGENTS.md`, `CLAUDE.md`, `CONTEXT.md`, `docs/agents/`, and skill configuration;
2. present the proposed workspace files, templates, and registration block;
3. let the user revise or approve the proposal;
4. write only agreed files and preserve existing content.

The portable `skills/` directory is developed here and can be copied or installed beside Matt's collection in another project. It may recommend or invoke `grill-with-docs`, `research`, `prototype`, `tdd`, and `code-review`, but it does not copy or fork those skills. The root `CONTEXT.md` and this design spec are development documents, not runtime dependencies of the delivered skills.

## Implementation order

The implementation plan intentionally builds all user-facing entries as self-contained Markdown workflows before reviewing the collection as a whole. Review covers initialization, Dataset setup, model registration or implementation, Benchmark/Experiment specs, local execution guidance, phase-level evaluation, structured record templates, and rendered reports. No Python package, CLI, or repository-specific runtime is part of v1.
