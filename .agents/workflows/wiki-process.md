---
description: Extract and structure raw sources into organized processed documents with grilling feedback and full content analysis.
---

# /wiki-process

Process one or more raw sources into structured documents in `02-processed/`.

## Usage

```
/wiki-process <filepath>                    # single file
/wiki-process --tag <tag>                   # all unprocessed entries with this tag
/wiki-process --domain <domain>             # all unprocessed entries in this domain
```

## Steps

1. **Read `registry.md`** and **`schema.md`** — load all canonical domains, tags, concepts, and components before writing anything

2. **Resolve targets** — from filepath or filter:
   - Single file: find its `01-raw/index.md` entry
   - Filter: grep `01-raw/index.md` for entries with `status: unprocessed` matching the tag/domain
   - Report targets to user: "Processing N source(s): [list]. Proceed?"

3. **For each target source:**

   a. **Update raw index status** → `in-progress`

   b. **Light sniff** — re-read just enough to prime context (even if already ingested):
      - PDF: pages 1-3
      - Markdown / Chat / Website: first 100 lines
      - Brain Dump: first 50 lines

   c. **First grill round** — based on sniff + any notes from `/wiki-ingest`, ask 2-3 questions before reading further:
      - "What's the most important concept to capture from this source?"
      - "Are there sections to skip or treat as low-priority?"
      - "Any cross-links you already know this should have?"
      Wait for answers before proceeding.

   d. **Full content read** — use the appropriate strategy:
      - PDF: read all pages using `pages` parameter in batches if large
      - Markdown: read full file
      - Chat: read full file
      - Website: read full file
      - Brain Dump: Treat information as incomplete, and potentially in-accurate. Only use to structure and refine. DO NOT create hard facts based on these.
      - If images are encountered: read them visually alongside text

   e. **Share summary** — present 3-5 bullet points of what you found before writing anything:
      ```
      Summary of <source>:
      - <key finding 1>
      - <key finding 2>
      - <key finding 3>
      Proposed structure: overview.md [+ section-1.md, section-2.md if large]
      ```

   f. **Second grill round** — ask targeted follow-ups based on the summary:
      - "Does this structure match what you expected?"
      - "Any facts here that contradict something you already know?"
      - "Anything missing you'd expect to find in this source?"
      Wait for answers before writing any files.

   g. **Determine structure** — apply size rule (incorporating grill answers):
      - Under ~300 lines or 3 major topics → `overview.md` only
      - Over threshold → `overview.md` + one `section-N.md` per major topic

   h. **Create processed folder**: `02-processed/<source-name-kebab>/`

   i. **Write `overview.md`** with full frontmatter + Summary + Key Concepts + Sections list + Glossary

   j. **Write `section-N.md` files** (if needed) with frontmatter + Summary + Content + Glossary + Related

   k. **Update `01-raw/index.md`** entry: set `status: processed`, add `processed_at: 02-processed/<folder>/`

   l. **Append to `02-processed/index.md`**:
      ```markdown
      ## <source-name>
      - **filepath**: `02-processed/<folder>/`
      - **filepath_raw**: `<original raw path>`
      - **description**: <one-line from overview summary>
      - **concepts**: [concept1, concept2]
      - **components**: [component1, component2]
      - **tags**: [tag1, tag2]
      - **status**: `draft`
      - **wiki_pages**: `[]`
      ```

   m. **Append to `02-processed/log.md`** and `01-raw/log.md`

4. **Propose registry additions** for any new terms encountered — confirm before writing

5. **Backlog suggestions** — if full content read surfaced tangential topics clearly out of scope for this wiki's purpose, offer to park them with a suggested priority:
   ```
   Encountered tangential topics not central to this source:
   1. "cdk-vs-cloudformation" — IaC tooling mentioned in passing [suggested: if-needed]
   2. "service-mesh" — referenced briefly in networking context [suggested: distant]
   Add any to backlog? (list numbers / n)
   ```
   Only flag genuinely tangential concepts — not every unfamiliar term. Never auto-add, never research them.
   Priority guidance for suggestions:
   - `near` — directly adjacent to the wiki's scope, clearly useful soon
   - `distant` — related but no concrete need visible yet
   - `if-needed` — only tangentially connected, would need a specific forcing reason

6. **Report** — "Processed: [list of created files]. Review at `02-processed/<folder>/overview.md`. Add `> [!REVIEW]` callouts for feedback, then run `/wiki-finalize`."
