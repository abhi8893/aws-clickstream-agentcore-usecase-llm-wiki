# /wiki-finalize

Integrate one or more processed documents into `99-wiki/`, resolving all cross-links.

## Usage

```
/wiki-finalize <processed-folder>           # e.g. /wiki-finalize 02-processed/usecase-context/
/wiki-finalize --tag <tag>                  # all processed entries with status: reviewed/done
```

## Steps

1. **Read `registry.md``, `schema.md`**, `purpose.md`**, and `99-wiki/index.md`** — load full context

2. **Read the processed document(s)** — `overview.md` + all section files

2b. **Share a brief impact summary** before asking any decisions:
   ```
   Found in <source>:
   - <key concept / fact 1>
   - <key concept / fact 2>
   - <key concept / fact 3>
   Wiki impact: N new pages likely, M existing pages to update, K conflicts spotted.
   ```
   This primes the grill round so your answers are more informed.

3. **Search existing wiki** for overlap:
   - Grep `99-wiki/index.md` for matching tags, domains, concepts
   - Read frontmatter of any matching pages (do not read full body yet)
   - Identify: new pages needed, existing pages to update, potential conflicts

4. **Collect grill decisions** — batch all decisions that need human input BEFORE writing anything:

   Present as a numbered list:
   ```
   Before I write, I need your input on N decisions:

   1. [NEW PAGE vs UPDATE] The concept "server-side-tagging" could go on the existing
      [[gtm-concepts]] page or a new dedicated [[server-side-tagging]] concept page.
      Recommendation: new page (it's substantial enough). Your call?

   2. [CONFLICT] This source says Firehose max buffer = 15 min; [[firehose-buffering]]
      currently says 5 min. Which is correct?

   3. [REGISTRY] Adding "server-side-tagging" to registry Tags. Confirm?

   4. [NEW OVERVIEW] No "clickstream" domain overview exists. Create one now or defer?
   ```

5. **Wait for all answers** before writing a single file.

6. **Write wiki pages** — one per decision resolved, using the correct type subfolder and frontmatter schema from `schema.md`. Use `[[wikilink]]` syntax throughout.

7. **Update `99-wiki/index.md`** — add all new pages under their type sections

8. **Update `99-processed/index.md`** — set `wiki_pages: [<list of created/updated pages>]`, `status: done`

9. **Append to `99-wiki/log.md`**:
   ```markdown
   ## [YYYY-MM-DD] finalize | <source title>
   - Action: finalize
   - Pages created: [[page1]], [[page2]]
   - Pages updated: [[page3]]
   - Sources: [[02-processed/<folder>/overview]]
   - Notes: <summary of what was added>
   ```

10. **Report** — "Finalized. Created: N pages. Updated: M pages. Run `/wiki-lint` to health-check."
