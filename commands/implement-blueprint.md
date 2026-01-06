---
description: Implement blueprint from specification
argument-hint: "<spec-path>"
---

# Implement Blueprint

Creates all components defined in a blueprint spec: author subagent, reviewer subagent, command, and registry entry.

## Step 1: Read Specification

Read the artifact spec at `$1` and extract:
- Artifact type name
- Registry entry (patterns, policies, meta_type, etc.)
- Author subagent definition
- Reviewer subagent definition
- Command definition (if specified)
- Required policies (existing or new)

## Step 2: Validate Dependencies

Verify prerequisites:
- No pattern conflicts in existing registry.yaml
- Target directories exist (agents/, commands/)

**Validate policies exist:**

Check each policy referenced in the spec exists in `policies/`.

If any are missing, **stop** and report:
```
Missing policies: §[PREFIX1], §[PREFIX2]

Run these commands first:
  /repmat:create-policies PREFIX1
  /repmat:create-policies PREFIX2

Then re-run:
  /repmat:implement-blueprint $1
```

Do not proceed until all policies exist.

## Step 3: Create Author Subagent

```
Task(@repmat-subagent-engineer, "Create the author subagent defined in $1

Follow the spec's author outline:
- Use semantic naming (architect/engineer/editor)
- Include Required Policies from spec
- Define workflow for creating this artifact type
- Include output format with meta_type considerations:
  - stateless: just files
  - status: include **Status:** field
  - tasklist: include checkbox structure")
```

## Step 4: Create Reviewer Subagent

```
Task(@repmat-subagent-engineer, "Create the reviewer subagent defined in $1

Follow the spec's reviewer outline:
- Name: [artifact-type]-reviewer
- Include Required Policies from spec
- Define validation workflow
- Output format: PASS/FAIL with findings
- For status-based: can update status on approval")
```

## Step 5: Review Subagents

```
Task(@repmat-subagent-reviewer, "Review both subagents created for the $1 artifact type.
Validate against §AGENT and §WFT.3 standards.")
```

## Step 6: Create Command (if specified)

If the spec includes a command definition:

```
Task(@repmat-command-engineer, "Create the command defined in $1

Follow the spec's command outline:
- Orchestrate author and reviewer
- Handle meta_type appropriately:
  - status: check/update status field
  - tasklist: parse progress, support resume
- Include failure handling for reviewer rejection")
```

```
Task(@repmat-command-reviewer, "Review the command created for $1")
```

## Step 7: Update Registry

Add the artifact entry to the appropriate registry file:
- Plugin artifacts: `${CLAUDE_PLUGIN_ROOT}/registry.yaml`
- Project artifacts: `$CLAUDE_PROJECT_DIR/.claude/registry.yaml`

Use the exact YAML from the spec's registry entry section.

## Step 8: Report

Summarize implementation:
- Files created (with paths)
- Registry updated
- Review verdicts
- Any deviations from spec

**Next steps:**
- Test the new command: `/<command-name> <test-input>`
- Or manually invoke author: `Task(@<author>, "...")`

## Important Rules

- Create subagents before command (command references them)
- Review each component before proceeding
- Match meta_type handling in author output format
- For tasklist multiphase: author must output phase structure
- Update spec status to "Implemented" when complete
