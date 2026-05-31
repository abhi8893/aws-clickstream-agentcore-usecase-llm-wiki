# /wiki-refine

Incorporate `> [!REVIEW]` callout feedback from processed or wiki layer files.

## Usage

```
/wiki-refine <filepath>                     # single file (processed or wiki)
/wiki-refine --layer processed              # all processed files with open REVIEW callouts
/wiki-refine --layer wiki                   # all wiki files with open REVIEW callouts
/wiki-refine --all                          # both layers
```

## Steps

1. **Detect layer** from filepath or flag:
   - Path starts with `02-processed/` → processed layer
   - Path starts with `99-wiki/` → wiki layer
   - `--all` → scan both

2. **Find all `> [!REVIEW]` callouts** in target file(s):
   ```bash
   grep -rn "\[!REVIEW\]" <target>
   ```
   Report: "Found N open review items in M files."

3. **For each file with open reviews:**

   **Processed layer:** Read the file, understand each `> [!REVIEW]` callout, incorporate the feedback into the processed content, remove the callout block. If the feedback implies structural changes (new section, different framing), apply them. Then re-run the finalization flow for that processed document (same as `/wiki-finalize` from step 3 onwards).

   **Wiki layer:** Read the file, understand each `> [!REVIEW]` callout, apply the feedback directly to the wiki page content, update frontmatter `updated:` date, remove the callout block. Update `99-wiki/log.md`.

4. **Propose registry additions** for any new terms introduced by the feedback — confirm before writing.

5. **Report** — "Refined: [list of files]. N review callouts resolved. M new registry terms added."
