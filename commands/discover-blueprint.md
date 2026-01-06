---
description: Discover blueprint from existing files (green/brown)
argument-hint: "<name-or-pattern> [--preset <preset-name>]"
---

# Discover Blueprint (Green/Brown)

Analyzes existing files to infer blueprint configuration. Use when files exist but no WFT setup (registry, policies, agents) is in place.

## Step 0: Load Preset (if specified)

If `--preset <name>` provided:

1. Read `${CLAUDE_PLUGIN_ROOT}/presets/<name>.md`
2. Extract preset context for domain analyzer:
   - Detection Signals
   - Search Keywords
   - Investigation Focus
   - Defaults (meta_type, author_role, typical_patterns)

If preset not found, list available presets from `presets/` and ask user to confirm or proceed without.

## Step 1: Discover Existing Files

```
Task(@repmat-domain-analyzer, "Discover existing artifacts matching '$1':

[IF PRESET PROVIDED]
Use this preset context to guide discovery:
---
$PRESET_CONTENT
---

Apply preset guidance:
- Use Detection Signals to identify matching files
- Use Investigation Focus to analyze file contents
- Use Search Keywords if web research needed for current practices
- Use Defaults as starting suggestions (can be overridden by evidence)
[END IF]

1. **Find files**: Search for files matching the name or pattern
   - If preset provided: start with typical_patterns from preset
   - If name (e.g., 'backend-code'): search apps/**/*, packages/**/*
   - If pattern (e.g., '*.py'): search directly
   - If docs (e.g., 'spec'): search docs/*/*.md

2. **Report findings**:
   - File count and locations
   - Common parent directories
   - Suggested glob patterns

3. **Infer policies**: Analyze file contents for:
   - Language markers (imports, syntax) → §PY, §TS, etc.
   - Framework markers (FastAPI, React, CDK) → §BE, §FE, §CDK
   - If preset: use Policy Considerations as hints
   - Match against existing policies/ files

4. **Infer meta_type**: Check files for:
   - If preset: start with preset default, verify against evidence
   - `**Status:**` field → status (list unique values found)
   - `[ ]` or `[x]` checkboxes → tasklist
   - Phase headers (## Phase N) → multiphase
   - None of above → stateless

5. **Check conflicts**: Look for overlapping patterns in registry.yaml

Output discovery report:
| Field | Inferred | Confidence | Evidence |
|-------|----------|------------|----------|
| patterns | [...] | high/med/low | Found N files at... |
| policies | [...] | high/med/low | Detected framework... |
| meta_type | ... | high/med/low | Found status field... |
| author | ... | high/med/low | File type suggests... |
| preset_used | ... | - | [preset name or 'none'] |")
```

## Step 2: Present Discovery

Show user the inferred configuration:

```markdown
## Discovery Report: $1

**Files found:** N files matching pattern
**Suggested patterns:** `[inferred patterns]`

| Field | Inferred | Confidence | Override? |
|-------|----------|------------|-----------|
| patterns | `[...]` | high | [confirm/change] |
| policies | `[...]` | medium | [confirm/change] |
| meta_type | `[...]` | high | [confirm/change] |
| states | `[...]` | medium | [confirm/change] |
| author | `[...]` | high | [confirm/change] |
```

Ask user to confirm or override each inferred value.

## Step 3: Design Artifact Spec

```
Task(@repmat-architect, "Design artifact type specification for: $1

Using confirmed configuration:
- Patterns: [confirmed]
- Policies: [confirmed]
- Meta-type: [confirmed]
- States/tasklist_type: [confirmed]
- Author role: [confirmed]

Create spec at: docs/specs/blueprint-$1.md

Include:
- Registry entry (YAML) matching discovered patterns
- Author subagent outline
- Reviewer subagent outline
- Command outline
- Note: artifacts discovered from existing files")
```

## Step 4: Review Spec

```
Task(@repmat-spec-reviewer, "Review docs/specs/blueprint-$1.md

Validate:
- Patterns match discovered files
- Policies appropriate for detected framework/language
- Meta-type matches file content structure
- No conflicts with existing registry entries")
```

## Step 5: Report

Summarize:
- Files discovered and patterns inferred
- Confidence levels for each inference
- User overrides applied
- Spec location
- Review verdict
- Policies: existing vs new

**Next commands:**

If new policies needed:
```
/repmat:create-policies <prefix> docs/specs/blueprint-$1.md   # seeded from spec
/repmat:implement-blueprint docs/specs/blueprint-$1.md
```

If all policies exist:
```
/repmat:implement-blueprint docs/specs/blueprint-$1.md
```

## Important Rules

- Prefer high-confidence inferences, ask about low-confidence ones
- If no files found, recommend `/repmat:design-blueprint` instead
- Always check for registry conflicts before finalizing
- Policies must be created before implementation
- When `--preset` specified, load preset before discovery to guide analysis
- Available presets: service, frontend, mobile, desktop, cli, library, infrastructure, schema, code, document, spec, plan, requirements, runbook, api-docs, jupyter
