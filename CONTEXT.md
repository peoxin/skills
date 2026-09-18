# Deep Learning Research Assistant

This context defines the vocabulary for a user-directed, Python/PyTorch research assistant. It deliberately excludes the platform-style artifact graph and governance model from the superseded design.

## Core

**Research assistant**:
A user-directed collection of portable, Markdown-defined skills that supports literature work, model implementation, experiments, analysis, and technical writing while leaving research goals and consequential decisions with the researcher.
_Avoid_: Autonomous research system, research platform

**User-facing research skill**:
A directly invocable capability organized around a research task, with explicit inputs and outputs and no hidden session state.
_Avoid_: Pipeline stage, internal utility

**Convenience skill**:
A thin entry that recommends or sequences existing skills while preserving their separate files, rules, outputs, and confirmation points.
_Avoid_: Central orchestrator, hidden workflow engine

**Research library**:
A file-based local collection of literature records, source links or snapshots, analysis notes, resource indexes, and project terminology that skills can search and update alongside on-demand web research.
_Avoid_: Knowledge service, vector database

**Research library write policy**:
Search and synthesis results become durable local records only after an explicit user request or confirmation; machine-generated indexes may refresh without promoting unverified claims to facts.
_Avoid_: Automatic web archive, conversation memory

**V1 workspace scope**:
The first release targets a new or user-prepared research workspace and one researcher-controlled multi-GPU machine. Existing repository migration, cloud execution, and team synchronization are out of scope.
_Avoid_: Universal project integration, cluster platform

## Data and experiments

**Dataset revision**:
An identified revision of a raw or generated dataset source whose content identity, provenance, license/access conditions, and retention are fixed. It is not directly usable by training or evaluation.
_Avoid_: Dataset name, data path, processed split

**Dataset setup**:
A versioned declaration that derives usable inputs from a raw dataset revision through split selection or generation, filtering, sampling, preprocessing, augmentation, label mapping, and other data-use rules. Standard inputs are train, validation, and test; custom inputs may be named explicitly and unavailable inputs are recorded.
_Avoid_: Dataset view, data folder, eval split

**Dataset setup skill**:
A user-facing capability that inspects or creates a Dataset setup independently of model implementation and experiment execution, validates its derived inputs, and records the source dataset and transformation choices for reuse.
_Avoid_: Dataset loader, hidden preprocessing

**Benchmark spec**:
A reusable comparison contract for a task and research question, defining eligible Dataset setups, metrics, baselines, seed and aggregation rules, selection constraints, resource expectations, and required report outputs across multiple experiment specs.
_Avoid_: One run config, score table

**Experiment plan**:
The research design for a set of experiments, including questions, comparisons, intended benchmark coverage, evaluation approach, and resource envelope. It is not directly executable.
_Avoid_: Experiment config, run config

**Experiment spec**:
A fully resolved, executable declaration for one concrete experiment or phase, binding component revisions, Dataset setup, phase inputs, seed, resources, commands, and output locations. It may reference a Benchmark spec but never replaces it.
_Avoid_: Experiment plan, benchmark protocol, training config

**Experiment composition**:
The functional view of one experiment as `Result = f_eval(f_train(Model, Data_train, Hyperparameter_config), Data_test, Eval_metric)`, where train and test inputs are derived from a Dataset setup and every input, transformation, and function implementation has a fixed revision.
_Avoid_: Training job, benchmark number

**Experiment component revision**:
The fixed identity of an intentional, reusable input or implementation such as a model, Dataset setup, training function, evaluation function, metric, configuration, or dependency lockfile.
_Avoid_: Latest code, file version

**Execution context record**:
The immutable record of facts about one actual execution, including code commit, Python/PyTorch/CUDA and dependency identities, hardware and assigned devices, timestamps, resource observations, and phase status. It describes where and how an experiment ran rather than defining a reusable component.
_Avoid_: Component revision, environment default

**Experiment phase**:
An independently executable part of an experiment composition, such as training or evaluation, with its own inputs, status, outputs, and continuation conditions while retaining the parent experiment identity.
_Avoid_: Pipeline stage, complete run

**Experiment orchestrator**:
The `run-experiment` entry that prepares one confirmed run, allocates shared resources, calls the declared training and/or evaluation phase skills, preserves execution context, and writes the parent manifest. It does not own phase-specific training, metrics, or report logic.
_Avoid_: Training runner, evaluation implementation, hidden workflow engine

**Training phase skill**:
The `train-experiment` entry that executes or documents one training phase, produces checkpoints and training metrics, and writes a phase manifest. It can run directly or under the Experiment orchestrator.
_Avoid_: Complete experiment, evaluation phase

**Evaluation phase skill**:
The `evaluate-experiment` entry that consumes a declared checkpoint and Dataset input, checks compatibility, computes metrics, creates visualizations, and writes a report bundle. It can run directly or under the Experiment orchestrator.
_Avoid_: Training runner, parent experiment manifest

**Partial experiment**:
An experiment whose declared phases or outputs are incomplete, failed, cancelled, or not yet executed. Available artifacts may be inspected or used under explicit eligibility rules without being presented as a complete result.
_Avoid_: Successful experiment, missing run

**Runnable revision**:
A committed and locatable revision of every code, configuration, Dataset setup, and environment input required for a deliverable run. Draft or dirty-worktree material may support exploration but is not eligible for a formal run.
_Avoid_: Latest version, working copy

**Git-tracked experiment**:
An experiment whose specs, code, configuration, reports, and provenance references are committed or otherwise identified in Git. Large outputs may remain external and be referenced by stable metadata.
_Avoid_: Git commit, tracker run

**Local multi-GPU execution**:
The documented procedure for one researcher-controlled multi-GPU machine: observe devices, select and reserve resources, run approved commands, record assignments and context, and release resources without claiming cloud-provider support. The skill collection does not ship a scheduler or runtime.
_Avoid_: Cluster platform, cloud backend, bundled scheduler

**External checkpoint record**:
A provenance record for a checkpoint obtained outside the current training phase, including source, version or commit, file digest, format, license, local reference, and declared model information before it is used as an experiment input.
_Avoid_: Downloaded file, pretrained model name

**Evaluation-only experiment**:
An experiment composition with no local train phase that evaluates an external or previously produced checkpoint against a declared model implementation, Dataset setup, evaluation implementation, and metric.
_Avoid_: Fake training run, ad hoc evaluation

**Checkpoint compatibility check**:
A validation that a checkpoint's architecture, format, weights identity, input and label semantics, preprocessing, shape, dtype/device constraints, and source conditions match the proposed evaluation inputs.
_Avoid_: State-dict load, filename match

## Evidence and reports

**Canonical structured record**:
The authoritative machine-readable YAML or JSON representation of a spec, manifest, metric result, or report data product. Markdown or HTML is a rendered view and cannot become authoritative through direct editing.
_Avoid_: Notes file, formatted copy

**Report bundle**:
The paired machine-readable data and human-readable narrative produced from an experiment or analysis, including quantitative results, qualitative visualizations, failures, missingness, and links to inputs.
_Avoid_: Results table, dashboard

**Rendered research report**:
A human-readable Markdown, HTML, or other presentation generated from canonical structured records, with links to source records, figures, inputs, and unresolved or partial results.
_Avoid_: Authoritative result file, dashboard snapshot

**Report edit boundary**:
Canonical metrics, tables, provenance, and source links are changed only in structured records. A rendered report may contain explicitly marked manual narrative, but direct edits do not change the authoritative result.
_Avoid_: Freeform result editing, manual override

**Metric identity**:
The versioned definition and implementation contract of a metric, including direction, units, inputs, masking, reduction, and output semantics.
_Avoid_: Metric name, score

**Evaluation protocol**:
A versioned declaration of how experiment outputs become evidence, including selected Dataset setup inputs, metrics, comparisons, seed/statistics policy, tolerances, aggregation, and failure criteria.
_Avoid_: Scoring script, ad hoc evaluation

**Source record**:
A literature or resource entry containing source type, title/author or owner, URL or locator, access date, version/commit when available, citation data, and verification status.
_Avoid_: Search snippet, unverified fact

**Source analysis**:
A structured and human-readable analysis of a source record, with claims, mechanisms, implementation details, experimental details, limitations, and exact locators or links.
_Avoid_: Paper summary without provenance

**Evidence link**:
A reference from a report, proposal, or written claim to the source record, source analysis, experiment spec, execution context, or report data that supports it.
_Avoid_: Citation without locator, unsupported assertion

**Exploratory run**:
A low-cost or draft execution used to learn about a pipeline, model, dataset setup, or hypothesis. It is not formal benchmark evidence unless an explicit confirmed spec makes it eligible.
_Avoid_: Final result, successful run

**Smoke test**:
A low-cost run that checks whether a model, data, training, evaluation, or reporting path executes correctly; it does not by itself support a research claim.
_Avoid_: Preliminary result

**Method proposal**:
A falsifiable proposal for a model or training improvement that states motivation, mechanism, expected effect, baseline comparison, ablations, risks, and resource implications before implementation.
_Avoid_: Idea, guaranteed improvement

## Portability and initialization

**Opaque research component**:
A project- or user-provided model, Dataset setup, training function, evaluation function, or metric consumed through a declared interface without requiring generic research skills to understand its task-specific scientific semantics.
_Avoid_: Domain-complete adapter, universally comparable model

**Generic experiment contract**:
The task-neutral shape used by research skills to locate and execute model, data, training, evaluation, metric, configuration, and report components while leaving their domain semantics to the supplied implementation and declared metadata.
_Avoid_: Domain-complete API, universal task model

**Visualization hook**:
An optional project-provided function that adds task-specific qualitative plots or examples to a standard Report bundle without changing its machine-readable fields or provenance rules.
_Avoid_: Required domain pipeline, untracked figure

**Research workspace initializer**:
A prompt-driven, confirm-before-write capability that explores a target workspace, presents proposed research directories, instructions, templates, and skill registration, then creates only agreed files without silently overwriting existing content.
_Avoid_: Project migration, blind scaffold

**Research skill distribution**:
The portable `skills/` directory containing independently callable Agent Skill directories. It can be copied into another project or skill collection while leaving Matt's separately installed skills intact and unmodified.
_Avoid_: Project-local runtime, skill fork, runtime bundle

**Portable capability**:
A standard Agent Skill directory whose enforceable behavior lives in Markdown instructions and document templates, with agent-specific discovery and invocation handled by the runtime. No Python package or bundled execution service is required.
_Avoid_: Agent-independent platform, runtime bundle
