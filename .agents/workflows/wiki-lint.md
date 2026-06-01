---
description: Health-check all three wiki layers for registry compliance, broken links, and inconsistencies.
---

# /wiki-lint

Health-check the wiki across all three layers. Report findings as a numbered list with suggested fixes.

## Usage

```
/wiki-lint                                  # full lint
/wiki-lint --layer raw|processed|wiki       # lint a single layer
/wiki-lint --check <check-name>             # run a specific check
```

## Checks

Run all checks unless `--check` is specified. Report each finding as:
`[SEVERITY] Check name — file or page — suggested fix`

Severity levels: `ERROR` (breaks navigation/integrity), `WARN` (degrades quality), `INFO` (suggestions).

### Check 1: Registry compliance
Grep all frontmatter `domain:`, `tags:`, `concepts:` across all three layers.
Flag any value not found in `registry.md` canonical columns.
```
[ERROR] Non-canonical tag "de-pipeline" in 02-processed/usecase-context/overview.md
  → Replace with "data-pipeline"
```

### Check 2: Open review callouts
Search for unclosed `> [!REVIEW]` blocks across `02-processed/` and `99-wiki/`.
```
[WARN] Open REVIEW callout in 02-processed/kinesis-guide/section-2.md line 45
  → Run /wiki-refine 02-processed/kinesis-guide/section-2.md
```

### Check 3: Wiki index completeness
Every `.md` file in `99-wiki/` (except `index.md`, `log.md`, `.gitkeep`) must appear in `99-wiki/index.md`.
```
[ERROR] 99-wiki/concepts/server-side-tagging.md not in index.md
  → Add entry under ## Concepts
```

### Check 4: Orphan wiki pages
Pages with no inbound `[[wikilink]]` references from other wiki pages or `related:` frontmatter.
```
[WARN] 99-wiki/glossary/firehose-buffering.md has no inbound links
  → Add [[firehose-buffering]] reference in at least one concept or architecture page
```

### Check 5: Broken processed source links
All `sources_processed:` values in wiki frontmatter must point to existing files.
```
[ERROR] 99-wiki/architectures/gtm-pipeline.md sources_processed links to non-existent
  02-processed/gtm-docs/overview.md
  → Run /wiki-process on the raw source, or fix the path
```

### Check 6: Stale raw queue
Raw entries with `status: unprocessed` older than 7 days.
```
[INFO] 01-raw/index.md: "kinesis-dev-guide" unprocessed since 2026-05-24
  → Run /wiki-process 01-raw/pdfs/kinesis-dev-guide.pdf
```

### Check 7: Concept gaps
Concepts mentioned in wiki page bodies that lack their own `99-wiki/concepts/` page.
Scan body text for `[[<slug>]]` links that don't resolve to existing files.
```
[WARN] [[firehose-delivery-stream]] linked in 3 pages but no concept page exists
  → Create 99-wiki/concepts/firehose-delivery-stream.md
```

### Check 8: Missing frontmatter fields
All wiki pages must have: `type`, `title`, `domain`, `tags`, `status`, `created`, `updated`.
```
[ERROR] 99-wiki/patterns/lambda-firehose.md missing required field: domain
```

## Report format

```
/wiki-lint complete — N issues found (E errors, W warnings, I info)

ERRORS (must fix):
1. ...
2. ...

WARNINGS (should fix):
3. ...

INFO (optional):
4. ...
```
