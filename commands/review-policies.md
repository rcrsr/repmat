---
description: Review all policy files against META standards in parallel
---

# Review All Policies

Audit all policy documents in `policies/` directory against META policy standards.

## Step 1: Enumerate Policy Files

List all markdown files in the policies directory:

```bash
ls -1 policies/*.md
```

## Step 2: Parallel Review

For EACH policy file found, spawn a `repmat-policy-reviewer` subagent in parallel:

```
Task(@agent-repmat-policy-reviewer, "Review policies/{filename} against §META standards. Generate a structured report with metrics, findings by severity, and recommendations.")
```

Launch ALL policy reviews simultaneously using parallel Task calls in a single response.

## Step 3: Aggregate Results

After all reviews complete, produce a summary report:

```markdown
# Policy Review Summary

## Overview

| Policy  | Lines | Violations | Status    |
| ------- | ----- | ---------- | --------- |
| §PREFIX | X     | Y          | Pass/Fail |

## Critical Issues

[List any Critical severity findings across all policies]

## Recommendations

### High Priority

[Consolidated high-priority fixes across policies]

### Medium Priority

[Consolidated medium-priority fixes]

## Next Steps

1. [Specific action items]
2. [Specific action items]
```

## Execution Notes

- Use `run_in_background: true` for each Task if policies exceed 5 files
- Wait for all background tasks before aggregating
- A policy PASSES if it has zero Critical and zero High violations
- A policy FAILS if it has any Critical or High violations
