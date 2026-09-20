# Use Project-Native Python Quality

Python quality is an optional Utility capability and a required implementation concern for skills that write Python, not a new Experiment component or mandatory runtime dependency. Skills preserve established project instructions and tools, limit automatic writes to the selected scope, and propose a capability-based quality profile only when the project has no clear convention; this keeps generated code consistent without silently migrating the surrounding repository.

## Consequences

`python-quality` provides explicit configure, check, and fix modes, while implementation-producing Experimentation skills can run the same project-native checks without that skill being installed. Language and engineering checks remain separate from tensor, device, checkpoint, data, and distributed behavior owned by component contracts.
