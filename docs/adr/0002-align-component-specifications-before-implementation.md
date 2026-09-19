# Align Component Specifications Before Implementation

Every Dataset setup, Model, Benchmark, Experiment, and Result execution has a component-named Markdown specification that records its human-readable contract. Its alignment stage requires Matt's `$grill-with-docs`; the skill stops when that dependency is unavailable, writes the specification only after the design frontier is empty and the user confirms the shared understanding, and reopens the interview when implementation reveals a normative change.

The specification has no `draft` or `confirmed` status. Exact machine-consumed fields remain in YAML or JSON, while the Markdown contract, structured records, implementation, and checks must agree. A user-created Git commit fixes them together as one component revision, avoiding a separate approval state while preserving prior specifications through Git history.
