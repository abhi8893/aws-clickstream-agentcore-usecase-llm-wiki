# /wiki-process

Process one or more raw sources into structured documents in `02-processed/`.

## Usage

```
/wiki-process <filepath>                    # single file
/wiki-process --tag <tag>                   # all unprocessed entries with this tag
/wiki-process --domain <domain>             # all unprocessed entries in this domain
```

## Steps

1. **Read `registry.md`** and **`schema.md`** — load conventions before writing

2. **Resolve targets** — from filepath or filter:
   - Single file: find its `01-raw/index.md` entry
   - Filter: grep `01-raw/index.md` for entries with `status: unprocessed` matching the tag/domain
   - Report targets to user: "Processing N source(s): [list]. Proceed?"

3. **For each target source:**

   a. **Update raw index status** → `in-progress`

   b. **Full content read** — use the appropriate strategy:
      - PDF: read all pages using `pages` parameter in batches if large
      - Markdown: read full file
      - Chat: read full file
      - Website: read full file
      - If images are encountered: read them visually alongside text

   c. **Determine structure** — apply size rule:
      - Under ~300 lines or 3 major topics → `overview.md` only
      - Over threshold → `overview.md` + one `section-N.md` per major topic

   d. **Create processed folder**: `02-processed/<source-name-kebab>/`

   e. **Write `overview.md`** with full frontmatter + Summary + Key Concepts + Sections list + Glossary

   f. **Write `section-N.md` files** (if needed) with frontmatter + Summary + Content + Glossary + Related

   g. **Update `01-raw/index.md`** entry: set `status: processed`, add `processed_at: 02-processed/<folder>/`

   h. **Append to `02-processed/index.md`**:
      ```markdown
      ## <source-name>
      - **filepath**: `02-processed/<folder>/`
      - **filepath_raw**: `<original raw path>`
      - **description**: <one-line from overview summary>
      - **concepts**: [concept1, concept2]
      - **tags**: [tag1, tag2]
      - **status**: `draft`
      - **wiki_pages**: `[]`
      ```

   i. **Append to `02-processed/log.md`** and `01-raw/log.md`

4. **Propose registry additions** for any new terms encountered — confirm before writing

5. **Report** — "Processed: [list of created files]. Review at `02-processed/<folder>/overview.md`. Add `> [!REVIEW]` callouts for feedback, then run `/wiki-finalize`."
