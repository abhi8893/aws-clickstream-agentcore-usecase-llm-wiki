# /wiki-refine-terms

Registry hygiene command. Detects duplicates, alias drift, and inconsistencies in `registry.md` using fast grep/find-based scanning. Does NOT re-read all wiki files by default — use `--deep` to force full content analysis.

Distinct from `/wiki-lint` — this operates on the registry itself, not on frontmatter across files.

## Usage

```
/wiki-refine-terms                    # fast mode: grep-based registry audit only
/wiki-refine-terms --section tags     # scope to one section (domains|tags|concepts|components)
/wiki-refine-terms --deep             # deep mode: also scans all frontmatter files for usage evidence
/wiki-refine-terms --deep --section concepts  # deep + scoped
```

---

## Mode: Fast (default)

Reads `registry.md` once. All checks run purely on the registry content — no file I/O beyond that single read. Completes in seconds.

### Bash commands used in fast mode

```bash
# Extract all canonicals from a section
grep -A1 "^| " registry.md | grep -v "^--$"

# Find cross-section duplicates
grep "^| " registry.md | awk -F'|' '{print $2}' | sort | uniq -d

# Find deprecated terms still in active sections
grep "^| " registry.md  # compare Deprecated table vs active tables
```

### Fast checks

**Check A: Cross-section duplicates**
A canonical term appearing in more than one active section.
```
[DUPLICATE] "agentcore" in Domains AND Tags
  → Remove from Tags (already a Domain)
```

**Check B: Alias shorter than canonical**
Alias is shorter/simpler than its canonical — possible wrong canonical.
```
[ALIAS-REVIEW] alias "otel" → canonical "opentelemetry"
  → "otel" is industry-standard; consider swapping which is canonical
```

**Check C: Verbose canonical with short alias**
Canonical is unnecessarily long when the alias is the official abbreviation.
```
[VERBOSE-CANONICAL] "managed-streaming-kafka" aliased to "msk"
  → MSK is the AWS product name; consider renaming canonical to "amazon-msk"
```

**Check D: Near-duplicate entries (same section)**
Two canonicals describing the same concept under slightly different names.
```
[NEAR-DUPLICATE] concepts: "streaming-ingestion" vs "real-time-ingestion"
  → Likely the same idea; keep one, move other to Deprecated
```

**Check E: Deprecated term still active**
A term in the Deprecated table that still appears as a canonical or alias in active sections.
```
[STALE-DEPRECATED] "de-pipeline" in Deprecated — not found in active sections
  → Clean; no action needed
```

**Check F: Component with no description**
A component entry missing its description or parent field.
```
[INCOMPLETE] component "data-warehouse" has empty description
  → Add a one-line description
```

---

## Mode: Deep (`--deep`)

In addition to all fast checks, scans frontmatter across all three layers using grep/find. **Does not re-read full file bodies** — only extracts frontmatter field values via targeted grep.

### Bash commands used in deep mode

```bash
# Extract all tag values used across all layers
grep -rh "^tags:" 01-raw/index.md 02-processed/ 99-wiki/ \
  | sed 's/tags: //g' | tr ',' '\n' | tr -d '[]" ' | sort | uniq -c | sort -rn

# Extract all component values used
grep -rh "^components:" 01-raw/index.md 02-processed/ 99-wiki/ \
  | sed 's/components: //g' | tr ',' '\n' | tr -d '[]" ' | sort | uniq -c | sort -rn

# Find frontmatter values NOT in registry (non-canonical drift)
# (diff usage list against registry canonicals)

# Find registry terms with ZERO usage in any frontmatter (dead entries)
# (terms defined in registry but never actually used)
```

### Additional deep checks

**Check G: Non-canonical terms in frontmatter**
Tags/concepts/components used in frontmatter that are aliases or deprecated — should be canonical.
```
[DRIFT] "otel" used in tags: frontmatter of 02-processed/usecase-context/overview.md
  → Should be "opentelemetry" (canonical); "otel" is only an alias
```

**Check H: Registry terms with zero frontmatter usage**
Terms defined in registry that appear in no frontmatter anywhere — possibly dead weight.
```
[UNUSED] concept "medallion-architecture" defined in registry but used in 0 files
  → Keep (may be used soon) or remove if genuinely irrelevant to this project
```

**Check I: Frontmatter terms missing from registry entirely**
Terms appearing in frontmatter that are neither canonical nor alias in the registry.
```
[UNREGISTERED] "amazon-kinesis" found in tags: of 99-wiki/architectures/gtm-pipeline.md
  → Not in registry; add as alias for "kinesis-data-streams" or normalize
```

---

## Output format

```
/wiki-refine-terms [fast|deep] complete — N issues found

FAST CHECKS (registry-only):
  A. Cross-section duplicates: N found
  B. Alias-should-be-canonical: N found
  C. Verbose canonicals: N found
  D. Near-duplicates: N found
  E. Stale deprecated: N found
  F. Incomplete entries: N found

DEEP CHECKS (frontmatter scan):  ← only shown with --deep
  G. Drift (alias used as canonical): N found
  H. Unused registry terms: N found
  I. Unregistered terms in frontmatter: N found

Findings:
  1. [DUPLICATE] ...
  2. [NEAR-DUPLICATE] ...

Proposed changes:
  1. Remove "agentcore" from Tags
  2. Merge "real-time-ingestion" → alias of "streaming-ingestion", add to Deprecated

Apply all? (y / pick numbers e.g. "1,3" / skip)
```

After confirmation: apply changes to `registry.md` only. Then report: "Run `/wiki-lint` to check if frontmatter files need updating to match the revised registry."
