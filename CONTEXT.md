# Deep Learning Research Assistant

This context defines the vocabulary for a user-directed, Python/PyTorch research assistant. It deliberately excludes the platform-style artifact graph and governance model from the superseded design.

## Core

**Research assistant**:
A user-directed collection of portable, Markdown-defined skills that supports Reference investigation, model implementation, experiments, analysis, and technical writing while leaving research goals and consequential decisions with the researcher.
_Avoid_: Autonomous research system, research platform

**User-facing research skill**:
A directly invocable capability organized around a research task, with explicit inputs and outputs and no hidden session state.
_Avoid_: Pipeline stage, internal utility

**Capability module**:
A non-invocable source-layout category that groups independently callable skills by responsibility. The top-level groups are Research and Tooling; Research contains Investigation, Experimentation, and Writing areas, while Tooling contains independent development tools. A group does not route calls or own hidden workflow state.
_Avoid_: Router skill, pipeline stage, installed skill identity

**Reference collection**:
A file-based local collection of records for papers, code, datasets, documentation, and other research materials plus their analyses and reviews. Skills preserve an established project location; a new collection defaults to `references/` and is not an experiment component.
_Avoid_: Research library, knowledge service, vector database

**Reference material**:
The paper, document, code excerpt, or other source artifact obtained from a locator or local path and optionally retained alongside its Reference record. Complete third-party repositories, datasets, and model weights remain external unless a narrower user-requested artifact is selected; the license is recorded as provenance and does not itself block an explicit acquisition request.
_Avoid_: Reference record, search result, automatic archive

**Reference acquisition skill**:
The `add-references` entry that obtains user-selected Reference material from a network locator or local source and creates its Reference record using only metadata available from that direct source.
_Avoid_: Reference search, metadata research, Reference analysis

**Direct source**:
The network locator, local file or directory, or versioned checkout explicitly supplied by the user, together with metadata directly exposed by that source. Search results, citation chains, and unrelated secondary metadata services are not direct sources.
_Avoid_: Discovery chain, metadata enrichment, secondary lookup

**Reference artifact**:
A retained file or externally located source entry listed by a Reference record, with its source, storage location, media type, digest, and acquisition provenance when available. Multiple artifacts may belong to one Reference version.
_Avoid_: Reference record, untracked attachment, complete repository copy

**Reference collection write policy**:
Search and synthesis results become durable Reference records only after an explicit user request or confirmation; machine-generated indexes may refresh without promoting unverified claims to facts. Complete third-party code repositories remain external checkouts fixed by commit.
_Avoid_: Automatic web archive, conversation memory

**V1 workspace scope**:
The first release targets a new or user-prepared research workspace and one researcher-controlled multi-GPU machine. Existing repository migration, cloud execution, and team synchronization are out of scope.
_Avoid_: Universal project integration, cluster platform

## Data and experiments

**Dataset revision**:
An identified revision of a raw or generated dataset source whose content identity, provenance, license/access conditions, and retention are fixed. It is not directly usable by training or evaluation.
_Avoid_: Dataset name, data path, processed split

**Dataset derivation**:
A versioned declaration that derives usable inputs from a raw dataset revision through split selection or generation, filtering, sampling, preprocessing, augmentation, label mapping, and other data-use rules. Standard inputs are train, validation, and test; custom inputs may be named explicitly and unavailable inputs are recorded.
_Avoid_: Dataset derivation, Dataset view, data folder, eval split

**Logical component directory**:
A stable root-level directory for one reusable research component or family, such as `data/<dataset-id>`, `models/<model-id>`, or `benchmarks/<benchmark-id>`. Its name identifies the logical component, not a version; formal reproducibility comes from the recorded Git commit and paths.
_Avoid_: Version directory, dated snapshot, commit-named folder

**Component specification document**:
A Markdown contract named for a Dataset derivation, Model, Benchmark, or Experiment. It records user-aligned intent, behavior, boundaries, interfaces, and verification requirements, has no lifecycle status, and remains consistent with the implementation and any Experiment configuration. Run outputs are determined by the Experiment contract.
_Avoid_: Implementation plan, rendered YAML, draft approval record

**Dataset definition skill**:
A user-facing capability that defines a Dataset revision and Dataset derivation independently of model implementation and experiment execution, validates its derived inputs, and records the source dataset and transformation choices for reuse.
_Avoid_: Dataset definition skill, Dataset loader, hidden preprocessing

**Benchmark spec**:
A reusable data-and-metric evaluation contract that may combine multiple Dataset derivations, names the train, validation, test, or custom inputs derived from them, defines composition and input mapping, and fixes metric identities, aggregation, and optional qualitative outputs. It does not choose a model, baseline, seed, resource request, or concrete checkpoint.
_Avoid_: Baseline suite, one run config, score table

**Benchmark definition skill**:
The `define-benchmark` entry that creates or revises one self-contained Benchmark from Dataset derivation paths, including its Benchmark specification document, metric implementations, and optional visualizations, without selecting a model or concrete run.
_Avoid_: Experiment definition, evaluation run

**Experiment spec**:
A fully resolved declaration for one concrete experiment, binding the selected Model, Dataset derivations, optional Benchmark, environment configuration paths, optional training configuration, seed, resources, commands, phases, and output locations. Training configuration may be absent for evaluation-only work; an Experiment control commit fixes the complete declaration.
_Avoid_: Component specification document, benchmark protocol, training config

**Experiment definition skill**:
The `define-experiment` entry that consumes selected component paths to create one concrete Experiment definition and its configuration or custom phase code. It changes only the selected Experiment directory and never defines or revises the Model, Dataset, or Benchmark it references.
_Avoid_: Benchmark definition, experiment execution

**Experiment composition**:
The functional view of one experiment as `Result = f_eval(f_train(Model, Data_train, Hyperparameter_config), Data_test, Eval_metric)`, where Train may select Dataset derivation inputs directly and Evaluate uses the Benchmark's declared inputs and metrics; every input, transformation, and function implementation has a fixed revision.
_Avoid_: Training job, benchmark number

**Component snapshot**:
The contents of a reusable Model, Dataset derivation, or Benchmark as fixed by an Experiment control commit and component path. A component may also have ordinary user-created Git history, but it does not require an independent formal component commit.
_Avoid_: Latest code, branch-only reference, file version without a commit

**Experiment phase**:
An independently executable part of an experiment composition, such as training or evaluation, with its own inputs, status, outputs, and continuation conditions while retaining the parent experiment identity.
_Avoid_: Pipeline stage, complete run

**Experiment execution entry**:
The `run-experiment` entry that reads a fixed Experiment, prepares its declared environment using the target project's convention, executes its declared phases, and checks its declared outputs. It does not define environment formats, output formats, Result schemas, or unrequested execution records.
_Avoid_: Training phase skill, evaluation phase skill, hidden workflow engine

**Partial experiment**:
An experiment whose declared phases or outputs are incomplete, failed, cancelled, or not yet executed. Available artifacts may be inspected or used under explicit eligibility rules without being presented as a complete result.
_Avoid_: Successful experiment, missing run

**Runnable revision**:
A formal runnable revision is identified by a repository, a complete Experiment control commit SHA, and paths for the Experiment and every same-repository code, configuration, Dataset derivation, Model, Benchmark, and environment input required by the run. External repositories are identified by immutable commit SHA and paths; external resources use immutable locators and digests. Draft or dirty-worktree material may support exploration but is not eligible for a formal run.
_Avoid_: Latest version, working copy

**Git-tracked experiment**:
An experiment whose Experiment definition and declared same-repository dependency closure are fixed by one Experiment control commit. Large outputs may remain external only when their source, digest, and producing or consuming Experiment control commit are recorded.
_Avoid_: One undifferentiated commit, tracker run

**Experiment control commit**:
The immutable revision containing `EXPERIMENT.md`, its configuration, custom phase code, and declared same-repository dependency closure for one concrete run. It fixes the environment configuration paths used by the Experiment when those files are in the same repository. Its commit message summarizes the Experiment and component paths; the commit tree and declared records provide the authoritative snapshot.
_Avoid_: Component commit, result commit

**Result commit**:
A user-created Git commit containing the outputs declared by an Experiment and any files the user chooses to retain from the run. It links back to the Experiment control commit. It does not require a generic execution context, parent manifest, or fixed Result directory.
_Avoid_: Checkpoint file alone, mutable dashboard

**Component verification**:
The pre-run check that `run-experiment` has shown the exact submission set, received user confirmation, selected an Experiment control commit, and verified that `HEAD` still identifies that commit and the worktree is clean. It also checks the declared local paths and external immutable identities needed by the run.
_Avoid_: Trusting a branch name, checking only the commit message

**Local multi-GPU execution**:
The documented procedure for one researcher-controlled multi-GPU machine: read the Experiment's resource declaration, allocate suitable local devices using project instructions, run approved commands, and release resources without claiming cloud-provider support. The skill collection does not ship a scheduler or runtime.
_Avoid_: Cluster platform, cloud backend, bundled scheduler

**External checkpoint record**:
A provenance record for a checkpoint obtained outside the current training phase, including source, version or commit, file digest, format, license, local reference, and declared model information before it is used as an experiment input.
_Avoid_: Downloaded file, pretrained model name

**Evaluation-only experiment**:
An experiment composition with no local train phase that evaluates an external or previously produced checkpoint against a declared model implementation, one or more Dataset derivations selected by a Benchmark, and the Benchmark's metric implementations.
_Avoid_: Fake training run, ad hoc evaluation

**Checkpoint compatibility check**:
A validation that a checkpoint's architecture, format, weights identity, input and label semantics, preprocessing, shape, dtype/device constraints, and source conditions match the proposed evaluation inputs.
_Avoid_: State-dict load, filename match

## Evidence and reports

**Canonical structured record**:
A YAML or JSON record that is authoritative for exact machine-consumed fields in a component, manifest, metric result, or report data product. A Component specification document is authoritative for the component's user-aligned human contract; rendered reports remain presentation views and cannot change structured values through direct editing.
_Avoid_: Notes file, formatted copy

**Report bundle**:
The paired machine-readable data and human-readable narrative produced from an experiment or analysis, including quantitative results, qualitative visualizations, failures, missingness, and links to inputs.
_Avoid_: Results table, dashboard

**Rendered research report**:
A human-readable Markdown, HTML, or other presentation generated from canonical structured records, with links to Reference records, figures, inputs, and unresolved or partial results.
_Avoid_: Authoritative result file, dashboard snapshot

**Report edit boundary**:
Canonical metrics, tables, provenance, and source links are changed only in structured records. A rendered report may contain explicitly marked manual narrative, but direct edits do not change the authoritative result.
_Avoid_: Freeform result editing, manual override

**Metric identity**:
The versioned definition and implementation contract of a metric, including direction, units, inputs, masking, reduction, output semantics, and the Benchmark-owned implementation path.
_Avoid_: Metric name, score

**Evaluation protocol**:
The input-mapping and metric-evaluation portion of a Benchmark spec, including how one or more Dataset derivations become named inputs and how metric values are aggregated.
_Avoid_: Baseline suite, scoring script, ad hoc evaluation

**Reference record**:
A paper, code, dataset, documentation, or other research-material entry containing its type, title or owner, external locator when available, access date, version or commit when available, license, citation data, and artifact provenance. New records use `reference-*` identities and do not carry lifecycle or verification status; this early collection does not promise compatibility with superseded record formats.
_Avoid_: Search snippet, candidate status, legacy record contract, unverified fact

**Reference analysis**:
A structured and human-readable analysis or review of one or more Reference records, with claims, mechanisms, implementation details, experimental details, limitations, and exact locators or links.
_Avoid_: Paper summary without provenance

**Evidence link**:
An evidence link from a report, proposal, or written claim to the Reference record, Reference analysis, Experiment spec, declared Experiment output, or report data that supports it.
_Avoid_: Citation without locator, unsupported assertion

**Writing project**:
A coherent reader-facing report, paper, note, or other writing effort that lives directly under the project's chosen writing location. Its document names, type metadata, internal directories, and supporting files follow project and user conventions rather than a universal template.
_Avoid_: Report-type bucket, experiment report bundle

**Exploratory run**:
A low-cost or uncommitted execution used to learn about a pipeline, Model, Dataset derivation, or hypothesis. It is not formal benchmark evidence unless fixed Component revisions make it eligible.
_Avoid_: Final result, successful run

**Smoke test**:
A low-cost run that checks whether a model, data, training, evaluation, or reporting path executes correctly; it does not by itself support a research claim.
_Avoid_: Preliminary result

**Method proposal**:
A falsifiable proposal for a model or training improvement that states motivation, mechanism, expected effect, baseline comparison, ablations, risks, and resource implications before implementation.
_Avoid_: Idea, guaranteed improvement

## Portability and initialization

**Opaque research component**:
A project- or user-provided model, Dataset derivation, training function, evaluation function, or metric consumed through a declared interface without requiring generic research skills to understand its task-specific scientific semantics.
_Avoid_: Domain-complete adapter, universally comparable model

**Generic experiment contract**:
The task-neutral shape used by research skills to locate and execute model, data, training, evaluation, metric, configuration, and report components while leaving their domain semantics to the supplied implementation and declared metadata.
_Avoid_: Domain-complete API, universal task model

**Python project initializer**:
The user-invoked `initialize-python-project` utility that creates an empty-target project with the installed uv defaults, a fixed Ruff baseline, and a fixed Git ignore file. It owns only initial project setup and establishes no contract with other skills.
_Avoid_: Project migration, universal Python template

**Ruff repair skill**:
The `fix-python-quality` utility that runs Ruff through uv to fix and verify formatting and lint failures inside an explicit Python file or directory scope.
_Avoid_: Quality configuration profile, independent check mode

**Python documentation review skill**:
The `improve-python-documentation` utility that improves docstrings and comments inside an explicit Python file or directory scope without changing behavior.
_Avoid_: General code review, automatic contract design

**Framework integration skill**:
An optional, independently callable capability that implements a named framework within an already aligned Model or Experiment scope without owning or redefining that component's contract.
_Avoid_: Framework-specific Experiment owner, generic framework runtime

**Canonical model weight contract**:
The framework-neutral identity, naming, and load semantics of a Model's learned parameters, including mappings from framework-native checkpoints when required.
_Avoid_: Trainer checkpoint, checkpoint filename

**Visualization hook**:
An optional project-provided function that adds task-specific qualitative plots or examples to a standard Report bundle without changing its machine-readable fields or provenance rules.
_Avoid_: Required domain pipeline, untracked figure

**Experiment workspace initializer**:
A prompt-driven, confirm-before-write capability that explores a target workspace and creates only agreed experiment-component directories without owning Reference or Writing locations.
_Avoid_: Project migration, blind scaffold

**Research skill distribution**:
The `skills/` source tree groups independently callable leaf skills under Capability module directories. `npx skills` discovers and installs the leaves by their stable skill names; the repository does not provide a router skill or custom installer, and the `grill-with-docs` skill is a required dependency for component planning; the skill stops when it is unavailable.
_Avoid_: Project-local runtime, skill fork, runtime bundle

**Portable capability**:
A standard Agent Skill directory whose enforceable behavior lives in Markdown instructions and document templates, with agent-specific discovery and invocation handled by the runtime. No Python package or bundled execution service is required.
_Avoid_: Agent-independent platform, runtime bundle
