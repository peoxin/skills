# Deep Learning Research Assistant v1

Status: recalibrated
Audience: one researcher using Python and PyTorch on a researcher-controlled multi-GPU machine

## Purpose

This repository develops a user-directed research assistant, not an autonomous research system. Its deliverable is a module-organized `skills/` source tree of directly callable, Markdown-defined Agent Skills for Reference investigation, deep-learning experimentation, and technical writing.

The assistant may search, summarize, align component specifications, generate code, run static checks, run smoke tests, queue fixed Experiment revisions, and assemble reports. The researcher owns the research question, hypotheses, benchmark acceptance, interpretation, and final written claims.

## Skill source layout

Capability modules organize source directories; they are not skills or routers. Every leaf remains independently callable by its frontmatter name:

```text
skills/
  investigation/
    search-references/
    add-references/
    analyze-references/
  experimentation/
    initialize-experiment-workspace/
    dataset-setup/
    implement-model/
    define-benchmark/
    define-experiment/
    run-experiment/
    train-experiment/
    evaluate-experiment/
    propose-improvements/
  writing/
    write-report/
```

`utilities` is a planned fourth module for small independent tools such as PDF-to-Markdown conversion and Python style or formatting support. It has no directory or implemented skill yet. Module directories do not contain `SKILL.md` or module README files.

## Target project layout

`initialize-experiment-workspace` creates only the visible root-level experiment directories after confirmation:

```text
data/          # logical Datasets and Dataset setups
models/        # self-contained model implementations and tests
benchmarks/    # self-contained data-and-metric protocols
experiments/   # concrete Experiment specs, including optional training settings
results/       # execution results, manifests, reports, figures, and artifacts
```

Component skills create subdirectories lazily:

```text
data/<dataset-id>/setups/<setup-id>/
  SETUP.md
models/<model-id>/MODEL.md
benchmarks/<benchmark-id>/
  BENCHMARK.md
  metrics/
  visualizations/
experiments/<experiment-id>/
  EXPERIMENT.md
  experiment.yaml
results/<experiment-id>/<result-id>/
  EXECUTION.md
```

Do not create top-level `training/`, `evaluation/`, `metrics/`, `runs/`, `reports/`, `common/`, or `_shared/` directories. Training and evaluation are Experiment phases; training settings belong in `experiment.yaml`; metric implementations belong to their Benchmark; and each model is self-contained.

Investigation and Writing skills first respect one clearly established project directory. They do not rename or migrate it; ambiguous candidates require user selection. With no existing convention, Investigation creates only the needed paths from:

```text
references/
  papers/<stable-id>/
    reference.yaml
    <retained-artifacts>
  code/<stable-id>/
    reference.yaml
    <retained-artifacts>
  materials/<stable-id>/
    reference.yaml
    <retained-artifacts>
  analysis/<reference-id>.md
  analysis/review-<topic>.md
```

Each Reference directory co-locates one `reference.yaml` with its retained artifacts. The record's `locator` stores a web URL or other external identity; its `artifacts` list records retained files and external materials with source, storage, media type, size, digest, and acquisition time. Complete third-party code repositories remain external checkouts identified by locator and immutable commit. A Reference collection may retain a small attributed code excerpt or a user-requested document snapshot, but not a complete third-party repository, model weights, or datasets. Licenses are recorded as provenance and do not block an explicit acquisition request.

With no existing writing convention, Writing uses `writing/<writing-id>/`. It assumes nothing about the main filename, document type, internal directories, or supporting files. Experiment evaluation reports remain under `results/`; reader-facing synthesis belongs to the Writing location.

## Scope

In scope:

- published-paper and preprint search;
- direct acquisition of user-selected References from remote locators and local files;
- GitHub, Hugging Face, official documentation, and high-quality resource search;
- Reference analysis, reviews, and evidence-linked notes;
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

Each entry is independently callable. They exchange explicit files or user-selected inputs, not hidden conversation state.

### Investigation

1. `search-references`
   Search papers, preprints, code, datasets, model pages, documentation, and other research materials. Present a shortlist and hand explicitly selected locators or paths to `add-references`.
2. `add-references`
   Acquire user-selected materials from direct sources and create one co-located `reference.yaml` plus retained or external artifact entries per Reference. It does not search for missing metadata or write lifecycle status.
3. `analyze-references`
   Analyze selected Reference records, extract mechanisms and experimental details with locators, and assemble comparisons or reviews.

### Experimentation

1. `initialize-experiment-workspace`
   Explore a target workspace, show the proposed experiment files and conventions, obtain confirmation, then create the minimum experiment workspace without overwriting existing content.
2. `dataset-setup`
   Register a raw Dataset revision and create or validate `SETUP.md`, structured records, and implementation describing inputs, splits, preprocessing, sampling, label mapping, and derived input digests.
3. `implement-model`
   Handle either `reproduce` or `new-method` intent. Align `MODEL.md`, including evidence or hypothesis separation, interfaces, required behavior, integration, and verification, before implementation.
4. `define-benchmark`
   Create or revise one self-contained data-and-metric Benchmark from committed Dataset setup revisions. Own `BENCHMARK.md`, its structured record, input composition, metric implementations, aggregation, optional visualizations, validation, and proposed component commit without selecting a Model or run.
5. `define-experiment`
   Create `EXPERIMENT.md` and one machine-readable Experiment record from committed Model, Dataset setup, and Benchmark revisions. Bind component commits, optional training settings, phases, seed, resources, checkpoint, and outputs without modifying its inputs.
6. `run-experiment`
   Align `EXECUTION.md` and orchestrate a fixed Experiment revision through the local multi-GPU procedure. Allocate resources, call the training and evaluation entries, preserve shared execution context, handle cancellation and partial states, and write the parent manifest.
7. `train-experiment`
   Execute or document the training phase, produce checkpoints and training metrics, and write a training phase manifest. It can be called directly or by `run-experiment`.
8. `evaluate-experiment`
   Execute or collect an evaluation phase, check checkpoint compatibility, and generate canonical metric/report records plus rendered reports, tables, plots, and optional visualization-hook outputs. It can be called directly or by `run-experiment`.
9. `propose-improvements`
   Combine Reference analyses and experiment reports into evidence-linked improvement proposals with hypotheses, mechanisms, risks, and ablations.

### Writing

1. `write-report`
   Produce an editable reader-facing report or paper from Reference records and analyses, experiment results, and user-confirmed claims. It does not rewrite canonical evidence, submit, or review the document.

## Component specification workflow

Creating or normatively changing a Dataset setup, Model, Benchmark, Experiment, or Result execution starts by invoking Matt's `$grill-with-docs`. This is a hard dependency for those planning stages: if it is unavailable, the component skill stops before specification or implementation work. The interview follows the design tree until its frontier is empty, then the assistant presents the complete shared understanding and waits for explicit user confirmation.

After confirmation, the component skill writes its component-named Markdown specification before implementation: `SETUP.md`, `MODEL.md`, `BENCHMARK.md`, `EXPERIMENT.md`, or `EXECUTION.md`. These specifications have no `draft` or `confirmed` status. If implementation reveals a normative change to behavior, interfaces, input semantics, metrics, phases, resources, verification requirements, or failure conditions, the skill pauses and repeats `$grill-with-docs`; formatting, typo fixes, and mechanical edits that preserve the specification do not reopen the interview.

Each component skill defines a short, component-specific section template in its own `SKILL.md`. The user-created Component commit fixes the Markdown specification, structured records, implementation, and checks as one consistent revision. Later normative changes create a new aligned revision; Git history preserves the prior contract.

## Shared records

The component-named Markdown specification is authoritative for human-readable intent, behavior, boundaries, interfaces, and verification requirements. YAML or JSON is authoritative for exact machine-consumed fields in component records, manifests, metrics, and report data. The formats must agree, but Markdown does not duplicate the complete structured record. Markdown or HTML reports rendered from canonical results remain presentation views; direct edits to them do not change structured values.

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

`define-benchmark` is the only entry that creates or revises a Benchmark. It consumes committed Dataset setup revisions, keeps `BENCHMARK.md`, the machine-readable Benchmark record, metric implementations, and visualizations inside the Benchmark directory, verifies their consistency, and proposes a user-created Benchmark Component commit. `define-experiment` consumes that fixed revision and writes only the selected Experiment directory. If the Benchmark needs to change, Experiment definition stops until `define-benchmark` produces a new aligned Component commit.

The functional model of a complete experiment is:

```text
Result = f_eval(
    f_train(Model, Data_train, Hyperparameter_config),
    Data_test,
    Eval_metric,
)
```

`Data_train` and `Data_test` are named Benchmark inputs and may be composed from multiple Dataset setups.

Every formal component is Git-addressed. A component revision records a repository, a complete 40-character commit SHA, and the paths containing its aligned specification, structured records, implementation, and checks. A Benchmark directory is self-contained, including its metric implementations and optional visualizations; Model directories are likewise self-contained and do not use a shared Model directory. An execution context records the observed HEAD and all component commits used in one run; it does not replace them with a separate execution snapshot. Environment facts may change between runs; each run gets its own context record.

### Phases and partial work

An Experiment spec may contain independent `train` and `evaluate` phases. `run-experiment` orchestrates only the phases declared in the spec by calling `train-experiment` and/or `evaluate-experiment`. Training produces checkpoints and training metrics; evaluation consumes a specifically identified checkpoint and the Dataset inputs selected by the Benchmark. Training settings are part of the Experiment spec and may be absent or not applicable for evaluation-only work. Evaluation may run against an incomplete training output when the spec permits it, but the report marks the training phase as partial, failed, cancelled, or incomplete. Missing phases are never silently treated as zero or success.

An external checkpoint is registered with an External checkpoint record and evaluated through an Evaluation-only experiment. The compatibility check validates architecture, format, weights digest, input/label semantics, preprocessing, shape, dtype/device constraints, and source/license information before formal evaluation.

### Formal and exploratory work

Formal version contract:

- Model, every Dataset setup selected by the Benchmark, Benchmark (including its metric implementations), dependency lockfiles, and the Experiment directory must be identified by Git repository, complete commit SHA, and paths. Each component's Markdown specification, structured records, implementation, and checks must agree at that revision. The Experiment control commit fixes `EXPERIMENT.md`, `experiment.yaml`, and its training and evaluation phase settings. For an external repository, also record its URL or identifier and how the component is used; verify it from a checkout or other immutable source before execution.
- Before a formal run, each source checkout must be clean for its declared source paths. For every component, compare the checkout paths with `git show <component.commit>:<path>`; a mismatch blocks the run.
- Record the Experiment control commit containing consistent `EXPERIMENT.md` and `experiment.yaml`. Record the observed `git rev-parse HEAD` in the execution context for audit, but use component commits as the reproducibility binding.
- Check HEAD, source-path cleanliness, and component-path equality before each phase and after each child phase returns. A change blocks the next phase and is written to the manifest.
- After a complete or partial run, commit `EXECUTION.md`, canonical manifests, execution context, metrics, report data, rendered reports, and figure metadata as a user-created Result commit. Skills propose the files and commit message but do not commit automatically.

Uncommitted or dirty work may support exploratory runs and smoke tests, but it is not eligible for a formal benchmark or report claim.

Formal execution requires an Experiment control commit and an aligned `EXECUTION.md`. The assistant may prepare code, validate records, and run low-cost smoke tests before a Component commit. It may not silently change a component, resource request, Dataset setup, or Benchmark scope.

## Local execution

The first execution target is one researcher-controlled multi-GPU machine. `run-experiment` observes local device availability, accepts explicit GPU count and device constraints, explains queue or reservation steps, creates the shared execution context, delegates phase work, records assignments, supports cancellation, and releases resources after success or failure. Child phase skills do not create a second reservation when called by the orchestrator. There is no cloud implementation or bundled scheduler in v1.

Large checkpoints, datasets, logs, and image collections need not be committed to Git. Their manifests record path or external reference, size, SHA256, generation/download time, and the component commits that produced or consumed them. Dataset processing code, Dataset setup records, checkpoint registration records, specs, code, configs, reports, and small provenance records are Git-tracked.

## References and investigation

`search-references` presents candidates and does not create durable records. An explicit request in the same conversation can hand selected locators or paths to `add-references`, which creates the new co-located Reference directories. `add-references` reads only direct sources, records optional metadata when directly available, and lists every retained or external material in `artifacts`; it does not write `verification`, `unresolved`, or `match_rationale`. `analyze-references` consumes the current co-located records and their artifacts. This early collection does not migrate or promise compatibility with superseded Reference formats. Search snippets and unverified summaries are never silently promoted to facts. The file-based Reference collection is updated only after explicit user request or confirmation.

## Evidence and writing

Numbers, comparisons, data-processing descriptions, and Reference-derived facts in reports link to canonical Reference or experiment records. Improvement proposals distinguish reported evidence, inference, and new hypothesis. `write-report` can add discussion and narrative under the chosen Writing project, but authoritative Reference and experiment records remain unchanged. The output is an editable document, not a submission package.

## Initialization and distribution

`initialize-experiment-workspace` follows a confirm-before-write interaction:

1. explore the target directory, instructions, domain docs, Git state, and existing experiment directories;
2. present the proposed experiment directories, starter files, and registration block;
3. let the user revise or approve the proposal;
4. write only agreed files and preserve existing content.

Install the nested leaf skills with `npx skills`, which preserves their frontmatter names as installed identities. The repository does not provide an installer or flattened export and does not promise that copying the grouped source tree directly into an agent's skill directory will work. Component planning requires the separately installed `grill-with-docs`; it stops if that skill is unavailable. The collection may also invoke `research`, `prototype`, `tdd`, and `code-review`, but does not copy or fork those skills. The root `CONTEXT.md` and this design spec are development documents, not runtime dependencies of the delivered skills.

## Implementation order

The initial implementation keeps every leaf skill as a self-contained Markdown workflow before reviewing each Capability module and the collection as a whole. Review covers Reference investigation, experiment initialization, Dataset setup, Model registration or implementation, separately defined Benchmark and Experiment specifications, local execution guidance, phase-level evaluation, structured record templates, and reader-facing writing. No Python package, custom installer, CLI, or repository-specific runtime is part of v1.
