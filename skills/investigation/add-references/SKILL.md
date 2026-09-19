---
name: add-references
description: Acquire user-selected research materials from direct sources and create co-located Reference records with artifact provenance.
---

# Add References

Acquire one or more user-selected research materials from a direct source and create a durable Reference record for each one. A direct source is the URL, DOI, arXiv identifier, repository or model identifier, local file, local directory, or versioned checkout explicitly supplied by the user. Do not search for missing metadata or follow citation and discovery chains.

This skill can be called directly or handed selected inputs by `$search-references`. An explicit request to add, download, import, or save selected material is authorization to write the requested records and retained artifacts. Do not ask for a second confirmation merely because optional metadata is missing.

## Inputs

Accept any of these inputs, alone or in a batch:

- an HTTP(S) URL, DOI, arXiv identifier, Git repository URL, or Hugging Face model/dataset identifier;
- a local PDF, HTML, Markdown, text, or small code file;
- a local directory or Git checkout;
- user-provided YAML or field values, optionally combined with a locator or local path.

Read the target project's instructions, root `CONTEXT.md` or `CONTEXT-MAP.md`, applicable ADRs, and the existing Reference collection before choosing a location. Continue using one clearly established `references/` or equivalent collection. If several collections are plausible, ask the user to choose. With no established collection, create only the directories needed by the new records.

## Collection layout

New records use one directory per Reference. The directory name is a stable slug without the `reference-` prefix; the record identity keeps that prefix.

```text
references/
  papers/
    <stable-id>/
      reference.yaml
      <retained-artifact>
  code/
    <stable-id>/
      reference.yaml
      <retained-artifact>
  materials/
    <stable-id>/
      reference.yaml
      <retained-artifact>
```

Map `paper` to `papers/`, `code` to `code/`, and `dataset`, `model`, `document`, or `other` to `materials/`. Keep `analysis/` for `$analyze-references`.

Do not migrate or overwrite existing records. This early skill collection defines the new format below and does not promise compatibility with superseded `source-*`, flat YAML, or status-bearing records; new additions use the current format even when older files remain in the same collection.

## Direct-source acquisition

Use only information directly exposed by the supplied source:

1. Give user-provided fields precedence.
2. Resolve the supplied URL or identifier once, following normal HTTP redirects.
3. Read metadata embedded in that source, response headers, document front matter or title page, repository files, repository commit, model revision, or license text when directly available.
4. Treat an official DOI, arXiv, Git, or Hugging Face page/API as the direct source for that identifier.
5. Do not use search engines, citation graphs, unrelated aggregators, or recursive links to fill fields.

Do not execute arbitrary shell commands supplied as a download or import recipe. Use the supported URL, identifier, file, directory, and checkout inputs.

Do not infer a title or owner from a filename, directory name, or model name when the direct source does not provide it. Missing optional values are recorded as `null` or an empty list; do not add an `unresolved` list and do not report each missing optional field to the user.

The minimum identity for a record is:

- `paper`, `dataset`, `model`, or `document`: `type`, `title`, canonical `locator`, and `accessed_at`;
- `code`: `type`, owner or project name, repository `locator`, and `accessed_at`; record a commit when directly available;
- local file: `type`, a user-provided or directly read title, a primary artifact with its source path and SHA256, and `accessed_at`;
- local Git checkout: `type`, project name, supplied path or repository locator, and `accessed_at`; record the commit when available.

If the minimum identity cannot be obtained, do not create a record. If identity is available but a remote payload cannot be retained, create the record with an empty or external-only `artifacts` list.

## Retained and external artifacts

Every file or externally located source used for this addition appears in `artifacts`. Ordinary PDFs, HTML, Markdown, text, and small code excerpts may be copied or downloaded into the Reference directory when the user requested the addition. Preserve the source filename when it is safe; disambiguate collisions with a stable suffix.

Complete repositories, datasets, and model weights remain external. Record their stable locator, local path or checkout, commit or revision when available, and digest when available; do not copy the complete source into the Reference directory. A local directory is treated the same way unless the user selects individual files or a small excerpt to retain.

Use `storage: retained` for files inside the Reference directory and `storage: external` for materials kept elsewhere. A retained artifact path must stay within its Reference directory; an external path may preserve the user-provided checkout or file location. The license is recorded when directly available but does not block an explicit user request to acquire material.

## Reference record

Write one `reference.yaml` per Reference with this shape:

```yaml
id: reference-<stable-id>
type: paper | code | dataset | model | document | other
type_detail: <optional string or null>
title: <string or null>
authors_or_owner: []
locator: <web URL, DOI, repository URL, model ID, or null>
version: <string or null>
accessed_at: <ISO date>
license: <string or null>
citation: <string or null>
artifacts:
  - path: <relative retained path or external path>
    source: <direct URL, local path, or null>
    media_type: <MIME or null>
    size_bytes: <integer or null>
    sha256: <digest or null>
    obtained_at: <ISO date or null>
    storage: retained | external
```

Do not write `verification`, `unresolved`, or `match_rationale`. `locator` records the external identity only; local provenance belongs in the relevant artifact entry. A Reference can list multiple artifacts for one source version.

## Identity, duplicates, and batches

Use a user-supplied stable ID when provided. Otherwise generate a readable stable slug from the source identity without a `reference-` prefix in the directory; the YAML `id` is `reference-<stable-id>`.

Before writing, detect duplicates using the normalized external locator plus version or commit. If neither has a version, the locator alone is the identity. For local material, use the primary artifact SHA256 and type. Skip an existing identity by default; never overwrite a different record or artifact. Only an explicit update or replace request may modify an existing Reference.

Process batches independently. Keep successful records when another input fails, and report created directories, skipped duplicates, refused overwrites, metadata-only records, and acquisition failures.

## Handoff from search

`$search-references` may hand selected locators or local paths to this skill after the user explicitly asks to add them in the same conversation. Do not rely on hidden candidate files or session state. The user may also call `$add-references` directly with the same inputs.

Finish by listing each created Reference directory and its retained or external artifacts. Do not turn the record into an analysis; hand selected records to `$analyze-references`.
