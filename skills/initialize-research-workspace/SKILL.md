---
name: initialize-research-workspace
description: Prepare a target project for the research skills by proposing a small file-based workspace and writing it only after confirmation.
---

# Initialize Research Workspace

Prepare the user's target project; do not treat this skill repository as the research project.

## Inspect first

Read the target project's Git status, root instructions, `AGENTS.md` or `CLAUDE.md`, existing domain docs, and any existing `.research/` content. Identify conflicts and conventions without writing files.

Propose only the directories the project needs:

```text
.research/
  library/       # confirmed source records and analyses
  datasets/      # Dataset revisions and Dataset setups
  benchmarks/    # reusable Benchmark specs
  experiments/   # concrete Experiment specs
  runs/          # phase manifests and artifact references
  reports/       # canonical report data and rendered reports
```

Show the exact files to create, existing files to preserve, and any optional registration text for the target project's agent instructions. Wait for confirmation. Create missing files only; overwrite a file only when the user names it explicitly.

## Starter document

When useful, create `.research/README.md` with this minimum content:

```markdown
# Research workspace

This directory stores explicit handoffs between research skills.

- Markdown: plans, source analyses, proposals, and human-readable reports.
- YAML/JSON: Dataset setups, Benchmark specs, Experiment specs, run manifests, metric records, and report data.
- Large datasets, checkpoints, logs, and figures may live elsewhere; records identify them by stable path or locator and digest.
- Formal results require confirmed specs and fixed component revisions. Exploratory work is labeled and cannot silently become benchmark evidence.
```

Do not generate model code, training scripts, cloud configuration, or a generic experiment runtime during initialization. Finish by listing created and preserved files and the next relevant skill.
