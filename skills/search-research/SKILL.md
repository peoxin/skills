---
name: search-research
description: Search papers, preprints, repositories, model hubs, documentation, and technical resources into verified candidate source records.
---

# Search Research

Clarify the research question, source classes, date/version constraints, and desired breadth. Search published papers, preprints, official project pages, GitHub, Hugging Face, official documentation, and high-quality technical writing as relevant.

Prefer primary sources. Treat search snippets and assistant summaries as discovery aids, not evidence. Verify that each candidate actually supports the stated relevance.

Present a shortlist before writing durable records. Save selected sources only after the user asks or confirms.

## Source record

Use this shape in Markdown, YAML, or JSON:

```yaml
id: source-<stable-id>
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

Keep published and preprint versions distinguishable. Record repository commits and model revisions when available. Do not turn a candidate list into a literature review; pass selected records to `$analyze-literature`.
