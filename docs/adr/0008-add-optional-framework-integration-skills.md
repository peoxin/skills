# Add Optional Framework Integration Skills

Framework support is provided by optional integration skills that implement already aligned component contracts. Each invocation works in one Model or Experiment scope without changing its contract; missing or changing contracts return to the corresponding `define-*` skill.

## Consequences

`integrate-pytorch-lightning` is the first integration skill. It defaults to Experiment scope and uses Model scope only when `MODEL.md` explicitly requires a framework-native Lightning model. Future frameworks receive parallel skills only when needed.
