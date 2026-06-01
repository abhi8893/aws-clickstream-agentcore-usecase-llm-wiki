---
description: Evolve the workflow itself based on user feedback about instructions, conventions, and process improvements.
---

# /workflow-improve

Incorporate user feedback to improve the llm-wiki workflow itself.

## Usage

```
/workflow-improve "<feedback>"
```

## Steps

1. **Understand the feedback** — ask clarifying questions if the feedback is ambiguous before making any changes.

2. **Identify which files to update:**
   - Workflow instructions → `.agents/workflows/<command>.md`
   - Structural conventions → `schema.md`
   - Registry terms → `registry.md`
   - Wiki purpose/scope → `purpose.md`
   - Claude entrypoint → `CLAUDE.md`

3. **Propose changes** — describe exactly what you'd change and why, before touching any file:
   ```
   Proposed changes:
   1. wiki-process.md: add step to handle images embedded in PDFs — read visually alongside text
   2. schema.md: update sniff strategy table to include PPTX files → read first 5 slides
   Proceed?
   ```

4. **Apply changes** after confirmation.

5. **Update `schema.md` log** or append a note in the relevant command file's header if the change is significant.

6. **Report** — "Workflow updated. Changed: [list of files]."
