# Use Component-Oriented Research Directories

The `sources/` and unified-workspace portions of this decision are superseded by ADR-0003. The experiment component directories and their ownership remain in effect.

The target research project uses visible root-level component directories: `sources/`, `data/`, `models/`, `benchmarks/`, `experiments/`, and `results/`. A Benchmark is a self-contained data-and-metric protocol that may compose multiple Dataset derivations and is owned by `define-benchmark`; an Experiment may consume a Benchmark, contains its optional training settings and phases, and is defined by `define-experiment`; a Result records one execution. We deliberately do not create top-level `training/`, `evaluation/`, `metrics/`, `runs/`, `reports/`, or shared model-code directories because these concepts belong to their owning component or phase.

Logical directory names are stable identities rather than versions. Git commits provide formal component revisions, and large data or execution artifacts may remain outside Git when their provenance and digest are recorded.
