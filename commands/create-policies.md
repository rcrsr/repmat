---
description: Create policies by extracting patterns from existing artifacts
argument-hint: <policy-prefix> [spec-or-pattern]
---

# Create Policies

Create policies by extracting patterns from existing artifact files.

## Step 1: Validate Input

- $1 = policy prefix (e.g., BE, FE, PY) (required)
- $2 = spec path OR file pattern (optional)
  - If `.md` file: read as artifact spec, extract patterns
  - If glob pattern: use directly (e.g., `apps/api/**/*.py`)
  - If omitted: prompt user for pattern

## Step 2: Identify Artifact Files

**If spec provided:**
```
Read artifact spec at $2
Extract file patterns from registry entry
```

**If pattern provided:**
```
Use $2 directly as glob pattern
```

**Find matching files:**
```
Glob for files matching pattern(s)
Report: Found N files matching [patterns]
```

## Step 3: Extract Patterns from Artifacts

```
Task(@repmat-domain-analyzer, "Extract policy rules from existing artifacts:

Files to analyze: [matched files from Step 2]

For each file, identify:
1. **Naming conventions** — file names, function/class names, variables
2. **Structure patterns** — imports, organization, module layout
3. **Code idioms** — common patterns, error handling, logging
4. **Framework usage** — how frameworks are used (FastAPI routes, React hooks)
5. **Documentation style** — docstrings, comments, type hints

Aggregate across all files:
- Most common patterns (>50% of files)
- Consistent conventions
- Anti-patterns to prohibit (inconsistencies found)

Output: Patterns report with frequency/confidence for each rule.")
```

## Step 4: Check Existing Policies

Read `policies/` directory:
- Find existing policies with similar scope
- Identify what can be reused vs created
- Note potential overlaps

## Step 5: Create Policy from Extracted Patterns

```
Task(@repmat-policy-engineer, "Create policy §$1 from extracted patterns.

Location: policies/policies-$1.md

Input:
- Extracted patterns: [from Step 3]
- Files analyzed: [N files]
- Existing policies: [from Step 4]

Create policy sections for each pattern category:
- {§$1.1} File Organization — extracted naming/structure rules
- {§$1.2} Code Patterns — extracted idioms with examples
- {§$1.3} Framework Usage — extracted framework conventions
- {§$1.4} Prohibited Patterns — anti-patterns found (inconsistencies)

For each rule:
- State the rule clearly
- Show example from analyzed files (anonymized, under 10 lines)
- Note frequency/confidence (e.g., 'found in 90% of files')

Follow §META structure with {§END} marker.")
```

## Step 6: Review Policy

```
Task(@repmat-policy-reviewer, "Review policies/policies-$1.md against §META.

Verify:
- Rules derived from actual artifact patterns
- Examples are real (from analyzed files)
- Self-contained (no external dependencies)
- Frequency/confidence noted for rules")
```

## Step 7: Report

Summarize:
- Policy created: `policies/policies-$1.md`
- Extracted from: [N files matching pattern]
- Rules defined: [count per category]
- Review verdict

**Next command:**
```
/repmat:implement-blueprint $2
```

## Important Rules

- Extract rules from actual artifact files, not assumptions
- Include frequency/confidence for each rule
- Examples must come from analyzed files (anonymized)
- Each policy must be self-contained (§WFT.6.2)
- Examples under 10 lines
- Policy must be created before implement-blueprint
