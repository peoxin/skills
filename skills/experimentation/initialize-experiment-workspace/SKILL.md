---
name: initialize-experiment-workspace
description: Prepare a target project for the deep-learning experiment skills by proposing component directories and writing them only after confirmation.
---

# Initialize Experiment Workspace

Prepare the user's target project; do not treat this skill repository as the research project.

## Inspect first

Read the target project's Git status, root instructions, `AGENTS.md` or `CLAUDE.md`, existing domain docs, and any existing experiment-component directories. Identify conflicts and conventions without writing files.

Inspect Git as a source of provenance, but do not create commits during initialization. The formal workflow requires the user to commit each component's Markdown specification, structured records, and implementation together before formal use; the user also creates the Result commit after a run. The initializer may document the repository identifier and expected paths in starter Markdown, but must not claim a component is fixed until a full commit SHA is available.

Create the complete set of root-level experiment directories after confirmation. Do not create component subdirectories; the component-specific skills create those lazily after the user names or confirms a component:

```text
data/            # logical Datasets and their Dataset setups
models/          # self-contained model components
benchmarks/      # self-contained data-and-metric protocols
experiments/     # concrete Experiment specs, including optional training settings
results/         # execution results, manifests, reports, figures, and artifacts
```

This skill does not create, choose, or modify a Reference collection or a writing directory. `$search-references`, `$analyze-references`, and `$write-report` follow the target project's existing conventions and create their defaults lazily when needed.

Directory conventions:

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

Do not create top-level `training/`, `evaluation/`, `metrics/`, `runs/`, `reports/`, `common/`, or `_shared/` directories. Training and evaluation are Experiment phases; training settings live in `experiment.yaml`; metric implementations belong to their Benchmark; each model is self-contained.

Show the exact files to create, existing files to preserve, and any optional registration text for the target project's agent instructions. Wait for confirmation. Create missing files only; overwrite a file only when the user names it explicitly.

## Starter document

Create `README.md` files in the root-level directories when useful. A root `README.md` may contain this minimum content:

```markdown
# Experiment workspace

This project uses component-oriented experiment directories.

- `data/`: logical Datasets, Dataset setups, processing code, and setup records.
- `models/`: self-contained model implementations, configurations, and tests.
- `benchmarks/`: self-contained data-and-metric protocols, metric implementations, and optional visualizations.
- `experiments/`: concrete Experiment specs with optional training settings and train/evaluate phases.
- `results/`: one directory per execution result, including manifests, reports, figures, and artifact references.
- Each component directory contains its component-named Markdown specification. The specification, structured records, and implementation are kept consistent and fixed together by a user-created Git commit.
- Large datasets, checkpoints, logs, and figures may live outside Git; records identify them by stable path or locator, source, size, SHA256, and relevant component commits.
- Formal results require fixed Git component revisions. Exploratory work is labeled and cannot silently become benchmark evidence.
```

Do not generate Reference or writing directories, model code, training scripts, cloud configuration, or a generic experiment runtime during initialization. Finish by listing created and preserved files and the next relevant experiment skill.
