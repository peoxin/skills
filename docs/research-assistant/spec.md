# Deep Learning Research Assistant v1

Status: recalibrated
Audience: one researcher using Python and PyTorch on a researcher-controlled multi-GPU machine

## Purpose

This repository develops a user-directed research assistant, not an autonomous research system. Its deliverable is a portable `skills/` directory of directly callable, Markdown-defined Agent Skills for literature work, model implementation, experiment definition and execution, evaluation, improvement proposals, and technical writing.

The assistant may search, summarize, generate plans and code, run static checks, run smoke tests, queue confirmed experiments, and assemble reports. The researcher owns the research question, hypotheses, benchmark acceptance, interpretation, and final written claims.

## Scope

In scope:

- published-paper and preprint search;
- GitHub, Hugging Face, official documentation, and high-quality resource search;
- source analysis, literature reviews, and evidence-linked notes;
- reproducing an existing model or implementing a model from a user idea through one shared model-implementation entry;
- explicit raw dataset records and reusable Dataset setups;
- reusable Benchmark specs and concrete Experiment specs;
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
   Create or revise Benchmark specs and concrete Experiment specs. Bind component revisions, Dataset setup, phases, seeds, metrics, resources, commands, and outputs without starting formal execution.
7. `run-experiment`
   Execute confirmed Experiment specs through documented local multi-GPU procedures. Show resource assignment, record execution context, retain phase outputs, and release resources; the skill collection does not ship a scheduler.
8. `evaluate-experiment`
   Execute or collect evaluation phases and generate canonical metric/report records plus rendered reports, tables, plots, and optional visualization-hook outputs.
9. `propose-improvements`
   Combine Source analyses, Research library records, and experiment reports into evidence-linked improvement proposals with hypotheses, mechanisms, risks, and ablations.
10. `write-report`
    Produce an editable paper or technical-report draft from confirmed sources, reports, and claims. It does not submit or review the document.
11. `research-assistant` (optional convenience entry)
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

The reusable comparison layer and one concrete run are different:

```text
Benchmark spec
  -> Experiment spec (one model/seed/config/phase instance)
  -> Experiment spec (another seed, baseline, or ablation)
```

The functional model of a complete experiment is:

```text
Result = f_eval(
    f_train(Model, Data_train, Hyperparameter_config),
    Data_test,
    Eval_metric,
)
```

Every component is locatable. A component revision is an intentional reusable input or implementation. An execution context record captures what actually happened in one run: commits, Python/PyTorch/CUDA and dependency identities, hardware, assigned GPUs, timestamps, resources, and phase status. Environment facts may change between runs; each run gets its own context record.

### Phases and partial work

An Experiment spec may contain independent `train` and `evaluate` phases. Training produces checkpoints and training metrics; evaluation consumes a specifically identified checkpoint and Dataset setup. Evaluation may run against an incomplete training output when the spec permits it, but the report marks the training phase as partial, failed, cancelled, or incomplete. Missing phases are never silently treated as zero or success.

An external checkpoint is registered with an External checkpoint record and evaluated through an Evaluation-only experiment. The compatibility check validates architecture, format, weights digest, input/label semantics, preprocessing, shape, dtype/device constraints, and source/license information before formal evaluation.

### Formal and exploratory work

All code, configurations, Dataset setups, metrics, evaluation implementations, and dependency/environment inputs for a deliverable run must be committed or otherwise locatable as Runnable revisions. Dirty or draft work may support exploratory runs and smoke tests, but it is not eligible for a formal benchmark or report claim.

Formal execution requires a user-confirmed Experiment spec and resource plan. The assistant may prepare code, validate records, and run low-cost smoke tests before confirmation. It may not silently change a component, resource request, Dataset setup, or benchmark scope.

## Local execution

The first execution target is one researcher-controlled multi-GPU machine. The skill observes local device availability, accepts explicit GPU count and device constraints, explains queue or reservation steps, records assignments, supports cancellation, and releases resources after success or failure. It has no cloud implementation or bundled scheduler in v1.

Large checkpoints, datasets, logs, and image collections need not be committed to Git. Their manifests record path or external reference, size, digest, generation time, and the component revisions that produced them. Specs, code, configs, reports, and small provenance records are Git-tracked.

## Literature and knowledge

`search-research` creates candidate Source records. `analyze-literature` consumes selected records and creates locator-bound Source analyses. A literature review aggregates selected analyses; search snippets and unverified summaries are never silently promoted to facts. The local Research library is file-based and is updated only after explicit user request or confirmation.

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
