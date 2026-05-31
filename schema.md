# Wiki Schema

Structural rules and conventions for all three layers. Read this file at the start of every wiki operation.
Also read `registry.md` for canonical terms before writing any frontmatter.

---

## Layer Overview

```
01-raw/          ← immutable source documents (never modify)
02-processed/    ← structured extractions, 1:1 with raw sources
99-wiki/         ← synthesized architecture guidance (the publishable output)
registry.md      ← global canonical terms (domains, tags, concepts)
purpose.md       ← why this wiki exists and what it's building toward
schema.md        ← this file
```

---

## 01-raw Layer

### Sub-folders

| Folder | Contents |
|---|---|
| `brain-dump/` | Your own rough notes, problem statements, ideas |
| `chats/` | LLM chat exports |
| `deep-research/` | Deep research output markdowns |
| `websites/` | Web-clipped articles (Obsidian Web Clipper output) |
| `pdfs/` | PDFs, whitepapers, AWS documentation |

**Rule:** Never modify files in `01-raw/`. They are the immutable source of truth.

### index.md format

Each entry registered via `/wiki-ingest`. Format:

```markdown
## [YYYY-MM-DD] <short-title>

- **filepath**: `01-raw/<subfolder>/<filename>`
- **filetype**: `pdf | markdown | chat | website`
- **status**: `unprocessed | in-progress | processed`
- **domain**: `[domain1, domain2]`
- **tags**: `[tag1, tag2, tag3]`
- **notes**: <human notes + anything for /wiki-process to consider>
- **processed_at**: `02-processed/<folder>/` ← filled in after /wiki-process
- **metadata**: <unstructured: page count, source URL, author, date>
```

### log.md format

```markdown
## [YYYY-MM-DD] <operation> | <source title>

- Action: ingest | update
- File: `01-raw/...`
- Notes: <what happened>
```

### Sniff strategies for /wiki-ingest

| File type | Strategy |
|---|---|
| PDF | Read pages 1-3 (cover + TOC + abstract/intro) |
| Markdown | Read first 100 lines |
| Chat export | Read first 20 lines + last 20 lines |
| Website (web clip) | Read first 100 lines |

---

## 02-processed Layer

One folder per source document. Folder name mirrors the source filename (kebab-case, no extension).

### Folder structure

```
02-processed/
  <source-name>/
    index.md       ← entry metadata (mirrors raw index entry, adds section list)
    overview.md    ← full structured summary of the source
    section-1.md   ← (only for large/complex sources)
    section-2.md
    ...
```

**Size rule:** If a source can be fully captured in `overview.md` under ~300 lines, do not create section files. Only section large/complex sources (whitepapers, long PDFs, deep-research docs).

### overview.md frontmatter

```yaml
---
type: processed-overview
title: "Human-readable title"
source_file: "../../01-raw/<subfolder>/<filename>"
source_type: pdf | markdown | chat | website
domain: [domain1, domain2]
tags: [tag1, tag2]
concepts: [concept1, concept2]
sections:
  - id: section-1
    title: "Section title"
    summary: "One-line summary"
status: draft | reviewed | done
created: YYYY-MM-DD
updated: YYYY-MM-DD
---
```

### overview.md body structure

```markdown
## Summary
2-3 sentences. What is this source about and why does it matter for the wiki?

## Key Concepts
- **ConceptA** — one-liner definition
- **ConceptB** — one-liner definition

## Sections
- [[section-1]] — one-line summary
- [[section-2]] — one-line summary

## Glossary
| Term | Definition |
|------|------------|
| Term | Definition |
```

### section-N.md frontmatter

```yaml
---
type: processed-section
title: "Section title"
parent: "[[overview]]"
domain: [domain1]
tags: [tag1, tag2]
concepts: [concept1]
status: draft | reviewed | done
created: YYYY-MM-DD
updated: YYYY-MM-DD
---
```

### section-N.md body structure

```markdown
## Summary
1-2 sentences covering this section.

## Content
<structured content>

## Glossary
| Term | Definition |
|------|------------|

## Related Sections
- [[section-N]]
```

### Inline feedback syntax

Use `> [!REVIEW]` callouts to leave feedback in processed files:

```markdown
> [!REVIEW]
> This section is missing the Firehose buffer window configuration. Add the 15-min max note.
```

LLM strips these blocks after incorporating feedback during `/wiki-refine`.

### index.md format (processed layer)

```markdown
## <source-name>

- **filepath**: `02-processed/<source-name>/`
- **filepath_raw**: `01-raw/<subfolder>/<filename>`
- **description**: <brief description from overview>
- **concepts**: [concept1, concept2]
- **tags**: [tag1, tag2]
- **status**: `draft | reviewed | done`
- **wiki_pages**: `[]` ← filled in after /wiki-finalize
```

### log.md format (processed layer)

```markdown
## [YYYY-MM-DD] <operation> | <source title>

- Action: process | refine | finalize
- Source: `02-processed/<folder>/`
- Wiki pages touched: []
- Notes: <what changed>
```

---

## 99-wiki Layer

### Folder structure

```
99-wiki/
  architectures/      ← architecture diagrams + narrative guidance
  concepts/           ← AWS services, technologies, ideas
  patterns/           ← reusable architecture patterns with tradeoffs
  decision-records/   ← ADR-style: what/why/alternatives rejected
  how-to-guides/      ← step-by-step implementation walkthroughs
  data-pipelines/     ← N-step pipeline design docs
  comparisons/        ← side-by-side option analysis
  glossary/           ← single-term definitions
  overviews/          ← one per domain (entry points)
  index.md
  log.md
```

### Naming conventions

- Files: `kebab-case.md`
- Architectures: `<solution-slug>.md` (e.g. `gtm-firehose-s3-pipeline.md`)
- Concepts: descriptive noun (e.g. `server-side-tagging.md`)
- Patterns: pattern name (e.g. `lambda-firehose-ingestion.md`)
- Decision records: `adr-<NNN>-<slug>.md` (e.g. `adr-001-msk-vs-kinesis.md`)
- How-to guides: imperative verb phrase (e.g. `instrument-agentcore-otel.md`)
- Data pipelines: `<pipeline-name>-pipeline.md`
- Comparisons: `<optionA>-vs-<optionB>.md`
- Glossary: single term slug (e.g. `firehose-buffering.md`)
- Overviews: `<domain>-overview.md`

### Base frontmatter (all wiki pages)

```yaml
---
type: architecture | concept | pattern | decision-record | how-to-guide | data-pipeline | comparison | glossary | overview
title: "Human-readable title"
domain: [domain1, domain2]
tags: [tag1, tag2]
sources_processed: ["[[02-processed/source-name/overview]]"]
related: ["[[wiki-page-slug]]"]
status: draft | reviewed | stable
created: YYYY-MM-DD
updated: YYYY-MM-DD
---
```

### Type-specific frontmatter extensions

**architecture:**
```yaml
aws_services: [kinesis-data-firehose, s3, redshift]
confidence: low | medium | high
scenario_variants: [S1.1, S2.1]
```

**decision-record:**
```yaml
decision: "One-sentence statement of the decision"
outcome: proposed | accepted | rejected | superseded
alternatives_considered: ["Option A", "Option B"]
```

**data-pipeline:**
```yaml
pipeline_type: batch | streaming | hybrid
stages: [ingestion, processing, storage, visualization]
```

**comparison:**
```yaml
options_compared: ["Option A", "Option B"]
recommendation: "Recommended option and one-line reason"
```

**glossary:**
```yaml
term: "Canonical term"
aliases: ["alias1", "alias2"]
```

**overview:**
```yaml
covers_domain: "domain-name"
child_pages: ["[[page-slug]]"]
```

### index.md format (wiki layer)

All pages grouped by type. Each entry:

```markdown
## Architectures
- [[gtm-firehose-s3-pipeline]] — GTM server-side → Firehose → S3 → Redshift pipeline

## Concepts
- [[server-side-tagging]] — GTM container running server-side to reduce client payload

## Patterns
...
```

### log.md format (wiki layer)

```markdown
## [YYYY-MM-DD] <operation> | <page title>

- Action: create | update | refine | query-save
- Pages: [[page-slug]]
- Sources: [[02-processed/source-name/overview]]
- Notes: <what changed and why>
```

### Cross-referencing rules

- Use `[[page-slug]]` syntax for all wiki-internal links
- `sources_processed` links back to the processed layer
- `related` links to other wiki pages in any type subfolder
- Every new concept mentioned in a page should either link to an existing concept page or be flagged for creation
- Glossary terms used in pages should link to their glossary entry on first use

### Contradiction handling

When sources contradict each other:
1. Note the contradiction in the relevant concept or architecture page
2. Create a `decision-record` to track the open question
3. Link both sources from the decision record
4. Update `status: proposed` → `accepted` once resolved

---

## Registry usage

Before writing any frontmatter:
1. Read `registry.md` Domains table — use only canonical domain names
2. Read `registry.md` Tags table — use only canonical tags; normalize aliases
3. If a genuinely new term is needed, propose it to the user before writing it to `registry.md`
4. Never silently introduce non-canonical terms

---

## Lint checklist

When running `/wiki-lint`, check:
- [ ] All `domain:` and `tags:` values exist in `registry.md`
- [ ] No open `> [!REVIEW]` callouts in processed or wiki files
- [ ] All wiki pages appear in `99-wiki/index.md`
- [ ] No orphan wiki pages (pages with no inbound `related:` or `[[wikilink]]` references)
- [ ] All `sources_processed:` links point to existing files
- [ ] Raw entries with `status: unprocessed` older than 7 days (stale queue warning)
- [ ] Concepts mentioned in wiki pages that lack their own concept page
