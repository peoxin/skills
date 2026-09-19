# Execute Fixed Experiments Through One Entry

`run-experiment` is the only user-facing execution entry. It reads the phases, commands, configuration, resources, checkpoint flow, outputs, and failure boundaries already fixed by `define-experiment`; it does not accept temporary execution overrides or create a second `EXECUTION.md` contract. Training and evaluation remain independent Experiment phases, but their execution, resource allocation, provenance, cancellation, and parent Result status belong to `run-experiment`.

Evaluation-specific compatibility checks, canonical metric data, visualizations, and rendered reports live in a reference file under the `run-experiment` directory so the main workflow remains concise without creating another callable skill.

Automatic component or Experiment commits are permitted only after the user explicitly confirms the exact diffs, paths, commit messages, and component bindings shown immediately before commit creation. A later or changed submission set requires new confirmation. Result commits remain user-created.

## Consequences

Users have one execution entry and one parent Result manifest. A Result records observed execution facts in machine-readable context and Result records rather than a separate `EXECUTION.md` specification. Formal execution still requires fixed Experiment and component revisions; explicitly authorized exploratory runs record their missing provenance and cannot be presented as formal evidence.
