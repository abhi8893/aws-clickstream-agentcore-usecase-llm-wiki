---
description: Search the wiki to answer questions with citations and optionally save answers as new pages.
---

# /wiki-query

Query the wiki and optionally save the answer as a new wiki page.

## Usage

```
/wiki-query "<question>"
/wiki-query "<question>" --save             # force save without asking
/wiki-query "<question>" --no-save          # suppress save prompt
```

## Steps

1. **Read `99-wiki/index.md`** — identify relevant page types and slugs from the question

2. **Read matching pages** — targeted reads based on index matches:
   - Match on tags, domain, title keywords
   - Read full body of the 3-5 most relevant pages
   - If a concept/pattern page exists for a key term in the question, always read it

3. **Synthesize answer** — write a structured response with:
   - Direct answer to the question
   - Citations as `[[wiki-page-slug]]` wikilinks
   - Tradeoffs or caveats if relevant
   - Gaps: explicitly state if the wiki doesn't yet have enough to answer fully

4. **Save-back prompt** (unless `--no-save`):
   ```
   This answer synthesizes [[page1]], [[page2]], [[page3]].
   Save as a new wiki page? Suggested type: comparison | architecture | concept | ...
   Suggested slug: <kebab-case-slug>
   (y / n / change type or slug)
   ```

5. **If saving:**
   - Write the answer as a proper wiki page with full frontmatter to the correct type subfolder
   - Set `sources_processed: []` if the answer draws on processed docs
   - Set `related: [<cited pages>]`
   - Add to `99-wiki/index.md` under the appropriate type section
   - Append to `99-wiki/log.md` with `Action: query-save`

6. **Report** — deliver the answer. If saved: "Saved as [[<slug>]]."
