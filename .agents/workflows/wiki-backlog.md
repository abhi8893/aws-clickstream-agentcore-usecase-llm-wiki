# /wiki-backlog

Manage the research backlog in `backlog.md`. Topics here are parked — tracked but not researched until explicitly promoted.

**Hard rule:** Never research, process, or create wiki pages for backlog items. They exist only to be tracked.

## Usage

```
/wiki-backlog add "<topic>" [context: "..."]   # append a new entry
/wiki-backlog list                              # show all current backlog topics
/wiki-backlog promote <slug>                    # promote a topic for research
/wiki-backlog remove <slug>                     # remove a topic (no longer relevant)
```

---

## Priority levels

| Value | Meaning |
|---|---|
| `near` | Parked temporarily — expected to be needed soon, just not right now |
| `distant` | Tangential encounter — no clear timeline, promote only when relevant work begins |
| `if-needed` | Very tangential — only promote with a concrete forcing reason |

---

## Sub-commands

### add

Append a new topic to `backlog.md`.

1. Normalise the topic into a kebab-case slug (e.g. "CDK vs CloudFormation" → `cdk-vs-cloudformation`)
2. Check `backlog.md` for an existing entry with the same or similar slug — if found, report and skip
3. Read `registry.md` — suggest classification values from canonical lists (optional, user can leave `-`)
4. Prompt for the required fields:
   ```
   Slug: cdk-vs-cloudformation
   One-liner: AWS CDK vs CloudFormation — IaC tooling choice

   Priority (near / distant / if-needed)?
   → Suggested: if-needed — IaC tooling is tangential to clickstream/AgentCore focus

   Classification (press Enter to skip any):
   Domain: [suggest from registry, e.g. web-app]
   Tags: [suggest from registry, e.g. — ]
   Concepts: [suggest from registry, e.g. — ]
   Components: [suggest from registry, e.g. — ]

   Context (why parked?): Encountered during AgentCore deployment research
   ```
5. Append row to the Topics table in `backlog.md`
6. Confirm: "Added `cdk-vs-cloudformation` [if-needed] to backlog."

---

### list

Read `backlog.md` Topics table and print it grouped by priority. No other file I/O.

```
NEAR (N topics):
  ...

DISTANT (N topics):
  ...

IF-NEEDED (N topics):
  ...
```

---

### promote <slug>

The user is ready to research a backlog item. This does NOT do any research itself — it prepares the handoff.

1. Find the entry in `backlog.md`, read its classification columns
2. Report the stored one-liner, priority, classification, and context to the user
3. Ask:
   ```
   Ready to research `<slug>`. How do you want to proceed?
     a) Drop a raw source into 01-raw/ and run /wiki-ingest
     b) Run /deep-research directly
     c) Create a wiki stub page now
   ```
4. After user chooses, remove the entry from `backlog.md`
5. Hand off to the appropriate workflow, pre-filling any known domain/tags/concepts/components

---

### remove <slug>

Remove a topic that is no longer relevant.

1. Find the entry in `backlog.md`
2. Confirm: "Remove `<slug>` — <one-liner>? (y/n)"
3. Delete the row after confirmation
