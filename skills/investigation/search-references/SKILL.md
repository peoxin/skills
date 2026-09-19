---
name: search-references
description: Search papers, code, datasets, documentation, and other research materials and save user-selected results as traceable Reference records.
---

# Search References

Clarify the research question, Reference classes, date or version constraints, and desired breadth. Search published papers, preprints, official project pages, GitHub, Hugging Face, official documentation, datasets, and high-quality technical writing as relevant.

Prefer primary sources. Treat search snippets and assistant summaries as discovery aids, not evidence. Verify that each candidate actually supports the stated relevance.

Present a shortlist before writing durable records. Save selected References only after the user asks or confirms.

## Reference collection

Read the target project's instructions and existing research-material directories before choosing a location. Continue using one clearly established `sources/`, `references/`, or equivalent collection without renaming or migrating it. If several directories could own the records, show the candidates and ask the user to choose.

When no relevant directory exists, use this default and create only the directories needed by the selected records:

```text
references/
  papers/
  code/
  materials/
  analysis/
```

- Put paper and preprint records in `papers/`.
- Put repository, model implementation, and other code records in `code/`.
- Put datasets, documentation, model pages, standards, blogs, and other records in `materials/`.
- Leave `analysis/` to `$analyze-references`.

For a new default collection, write one YAML file per Reference as `<class>/<reference-id>.yaml`. Preserve an established project's record format when it already has one. Existing `source-*` IDs remain readable; assign `reference-*` IDs to new records.

Record complete code repositories by locator and immutable commit and keep their checkouts outside the Reference collection. Small excerpts needed for analysis may be stored with their origin, commit, path, and license. Do not copy a complete third-party repository into `references/code/`. Save a PDF or other material snapshot only when the user explicitly requests it and its access and license permit retention. Keep model weights and datasets external and record their stable locators and provenance.

## Reference record

Use this shape for new records:

```yaml
id: reference-<stable-id>
type: paper | preprint | repository | model | dataset | documentation | blog
title: <title>
authors_or_owner: [<name>]
locator: <URL, DOI, arXiv ID, repository path, or model ID>
version: <publication version, tag, or commit; null if unavailable>
accessed_at: <ISO date>
license: <license or unknown>
verification: verified | partially-verified | candidate
match_rationale: <why it matters to the user's question>
citation: <citation data when applicable>
unresolved: [<missing field or verification question>]
```

Keep published and preprint versions distinguishable. Record repository commits and model revisions when available. Do not turn a candidate list into a review; pass selected records to `$analyze-references`.
