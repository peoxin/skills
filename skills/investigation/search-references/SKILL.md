---
name: search-references
description: Search papers, code, datasets, documentation, and other research materials, then hand selected sources to add-references.
---

# Search References

Clarify the research question, desired Reference types, date or version constraints, and breadth. Search published papers and preprints, official project pages, Git repositories, Hugging Face, official documentation, datasets, standards, and high-quality technical writing as relevant.

Prefer primary sources. Treat search snippets and assistant summaries as discovery aids, not evidence. Verify that each candidate actually supports the stated relevance before presenting it.

## Search and shortlist

Read the target project's instructions, root `CONTEXT.md` or `CONTEXT-MAP.md`, applicable ADRs, and the existing Reference collection before searching. Continue using one clearly established `references/` or equivalent collection. If several collections are plausible, ask the user to choose.

Present a shortlist before any durable write. For each candidate, show the available source identity, type, title or owner, locator, version or commit when directly available, and a concise explanation of why it appeared in the shortlist. Keep that relevance explanation in the conversation; it is not a field in the Reference record.

Candidates are not Reference records. Do not create candidate YAML, do not create Reference directories, and do not add lifecycle or verification status. The `search-references` skill is complete when it has presented the shortlist or handed selected inputs to the `add-references` skill.

## Explicit handoff

The user may skip adding anything. If the user explicitly says to add selected candidates, download a named document, or import a listed path, hand those selected locators or paths to the `add-references` skill in the same conversation. Examples of explicit intent include "add the first and third candidates" and "save this PDF in references". A vague request to search or discuss does not authorize a write.

Show the shortlist before handing off, even when the original request combines searching and adding. Ask which candidates to add when the selection is ambiguous. The `add-references` skill independently performs direct-source acquisition, creates the new per-Reference directories, and reports the resulting artifacts.

## Type vocabulary

Use these simple semantic types in the shortlist and handoff:

- `paper`: papers, preprints, and technical reports;
- `code`: repositories and implementations;
- `dataset`: datasets and their data descriptions;
- `model`: model pages, model revisions, and checkpoint provenance;
- `document`: official documentation, standards, blogs, tutorials, and web materials;
- `other`: anything that cannot reasonably use the above, with an optional `type_detail`.

Do not turn a candidate list into an analysis. Pass selected inputs to the `add-references` skill, then pass created records to the `analyze-references` skill when the user asks for evidence-linked reading.
