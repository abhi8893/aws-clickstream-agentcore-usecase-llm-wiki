# /wiki-ingest

Register a raw source document into `01-raw/index.md` using a light sniff.

## Usage

```
/wiki-ingest <filepath> [notes: "..."] [tags: tag1, tag2]
```

## Steps

1. **Read `registry.md`** — load canonical domains, tags, concepts, components before writing anything

2. **Detect file type** from extension:
   - `.pdf` → PDF
   - `.md` → markdown (check path: `brain-dump/` → brain-dump, `chats/` → chat, `deep-research/` → deep-research, `websites/` → website)
   - other → ask user

3. **Light sniff** — read just enough to infer metadata, never the full document:
   - PDF: read pages 1-3 (cover + TOC + abstract)
   - Markdown: read first 100 lines
   - Chat export: read first 20 lines + last 20 lines
   - Website (web clip): read first 100 lines

4. **Propose registration** — present inferred metadata to user for confirmation:
   ```
   Detected: <filetype>, <size estimate>
   Title: <inferred title>
   Domain: [<inferred domains>]
   Tags: [<inferred tags>]
   Components: [<inferred components — which parts of the system does this source relate to?>]
   Notes: <inferred one-line description>
   Any corrections or additions?
   ```

5. **Propose registry additions** — if any new canonical terms are needed across any section, present them:
   ```
   New terms to add to registry.md:
   - Tags: ["new-tag"] under <section>
   - Components: ["new-component"] — <description>, parent: <parent or —>
   - Concepts: ["new-concept"] — <brief definition>
   Confirm? (y/n / suggest alternative)
   ```
   Write to `registry.md` only after confirmation.

6. **Write index entry** to `01-raw/index.md` using the confirmed metadata:
   ```markdown
   ## [YYYY-MM-DD] <short-title>

   - **filepath**: `<filepath>`
   - **filetype**: `<type>`
   - **status**: `unprocessed`
   - **domain**: `[<domains>]`
   - **tags**: `[<tags>]`
   - **notes**: <notes>
   - **processed_at**: —
   - **metadata**: <size, source URL, author if known>
   ```

7. **Append to `01-raw/log.md`**:
   ```markdown
   ## [YYYY-MM-DD] ingest | <title>
   - Action: ingest
   - File: `<filepath>`
   - Notes: <brief description>
   ```

8. **Backlog suggestions** — if the light sniff surfaced tangential topics clearly outside the source's main scope, offer to park them with a suggested priority:
   ```
   Encountered tangential topics not central to this source:
   1. "cdk-vs-cloudformation" — IaC tooling mentioned in passing [suggested: if-needed]
   2. "service-mesh" — networking concept referenced briefly [suggested: distant]
   Add any to backlog? (list numbers / n)
   ```
   Only suggest genuinely tangential concepts — not every term in the source. If user confirms, run `/wiki-backlog add` for each. Never auto-add.

9. **Confirm** — report back: "Registered. Run `/wiki-process <filepath>` when ready to extract."
