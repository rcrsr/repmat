---
description: Modify existing blueprint configuration (brownfield)
argument-hint: "<blueprint-name>"
---

# Modify Blueprint (Brownfield)

Updates an existing blueprint's configuration — registry entry, agents, commands, or policies. Use when WFT setup exists but needs changes.

## Step 1: Load Current Configuration

Read the existing artifact configuration:

1. **Registry entry**: Load from `registry.yaml` (plugin or project)
2. **Author agent**: Load from `agents/[author].md`
3. **Reviewer agent**: Load from `agents/[reviewer].md`
4. **Command(s)**: Find commands that reference this artifact type
5. **Policies**: Load referenced policy files

Present current state:

```markdown
## Current Configuration: $1

**Registry:**
```yaml
$1:
  patterns: [current patterns]
  author: [current author]
  reviewer: [current reviewer]
  policies: [current policies]
  meta_type: [current meta_type]
  ...
```

**Components:**
- Author: `agents/[name].md` (N lines)
- Reviewer: `agents/[name].md` (N lines)
- Commands: `commands/[name].md` (if any)
- Policies: [list]
```

## Step 2: Identify Changes

Ask user what needs modification:

| Change Type | Affects |
|-------------|---------|
| Add pattern | Registry only |
| Change policies | Registry + agents (Required Policies) |
| Change meta_type | Registry + author output format |
| Add states | Registry (for status meta_type) |
| Rename author/reviewer | Registry + agent files + commands |
| Update workflow | Agent file only |
| Add command | New command file |

Get specific change request from user.

## Step 3: Design Changes

```
Task(@repmat-architect, "Design modifications to artifact type: $1

Current configuration: [from Step 1]
Requested changes: [from Step 2]

Create modification spec at: docs/specs/modify-$1.md

Include:
- Before/after registry entry
- Files to modify (with specific changes)
- Files to create (if any)
- Impact analysis (what else might break)
- Migration notes (if patterns change)")
```

## Step 4: Review Changes

```
Task(@repmat-spec-reviewer, "Review modification spec at docs/specs/modify-$1.md

Validate:
- Changes are minimal and focused
- No breaking changes to existing artifacts
- Pattern changes don't create conflicts
- Policy references remain valid")
```

## Step 5: Apply Changes

Based on approved modification spec:

**Registry changes:**
Update the appropriate registry.yaml file with new configuration.

**Agent changes:**
```
Task(@repmat-subagent-engineer, "Apply modifications to agents per docs/specs/modify-$1.md")
```

**Command changes:**
```
Task(@repmat-command-engineer, "Apply modifications to commands per docs/specs/modify-$1.md")
```

## Step 6: Verify

```
Task(@repmat-subagent-reviewer, "Verify modified agents still pass §AGENT standards")
Task(@repmat-command-reviewer, "Verify modified commands still pass §WFT.4 standards")
```

## Step 7: Report

Summarize:
- Changes applied
- Files modified (with diffs summary)
- Verification results
- Any manual steps required

## Important Rules

- Always show current state before proposing changes
- Prefer minimal, focused changes
- Warn about breaking changes (pattern modifications)
- Keep modification spec for audit trail
- If blueprint doesn't exist, recommend `/repmat:design-blueprint` or `/repmat:discover-blueprint`
