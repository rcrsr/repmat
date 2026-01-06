---
description: Design a new blueprint from scratch (greenfield)
argument-hint: "<blueprint-name> [--preset <preset-name>] [description]"
---

# Design Blueprint (Greenfield)

Creates a specification for a new blueprint when no existing files exist yet. For projects with existing files, use `/repmat:discover-blueprint` instead.

## Step 0: Load Preset (if specified)

If `--preset <name>` provided:

1. Read `${CLAUDE_PLUGIN_ROOT}/presets/<name>.md`
2. Extract preset defaults to pre-populate requirements:
   - Defaults (meta_type, author_role, typical_patterns)
   - Policy Considerations (suggested policy sections)
   - Search Keywords (for exploring best practices)

If preset not found, list available presets and ask user to confirm or proceed without.

## Step 1: Gather Requirements

Ask user to describe the artifact. If preset loaded, show defaults and ask to confirm or override:

1. **What is it?** — What work product does this artifact represent?
2. **Where will it live?** — File patterns (e.g., `docs/specs/*.md`, `apps/api/**/*.py`)
   - If preset: suggest `typical_patterns` from preset
3. **What rules govern it?** — Existing policies or need new ones?
   - If preset: show Policy Considerations as suggested sections
4. **What type of author?**
   - If preset: suggest `author_role` from preset
   - architect → creates specs/designs
   - engineer → creates code
   - editor → creates documents

## Step 2: Determine Meta-Type

If preset loaded, suggest `meta_type` from preset defaults. Otherwise ask user about state tracking:

| Meta-Type | When to Use | Example |
|-----------|-------------|---------|
| stateless | Code files, no lifecycle | `*.py`, `*.tsx` |
| status | Documents with approval flow | specs, requirements |
| tasklist | Progress tracking with checkboxes | plans, checklists |

If **status**: What states? (suggest: Draft, In Review, Approved, Rejected)
If **tasklist**: Single phase or multiphase?

## Step 3: Design Artifact Spec

```
Task(@repmat-architect, "Design an artifact type specification for: $1

Based on user requirements:
- Name: $1
- Patterns: [from Step 1]
- Policies: [from Step 1]
- Meta-type: [from Step 2]
- Author role: [from Step 1]

Create spec at: docs/specs/blueprint-$1.md

Include:
- Registry entry (YAML)
- Author subagent outline
- Reviewer subagent outline
- Command outline
- Design decisions with rationale")
```

## Step 4: Review Spec

```
Task(@repmat-spec-reviewer, "Review the blueprint specification at docs/specs/blueprint-$1.md

Validate:
- Blueprint completeness (type, author, reviewer, policies)
- Naming conventions (§WFT.3.2)
- Meta-type configuration is complete
- Policies referenced exist or flagged as new")
```

## Step 5: Report

Summarize:
- Artifact type designed
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

- This command is for greenfield scenarios — no existing files
- For existing files, recommend `/repmat:discover-blueprint` instead
- Always confirm meta_type before designing
- Policies must be created before implementation
- When `--preset` specified, use preset defaults as suggestions (user can override)
- Available presets: service, web, mobile, desktop, cli, library, infrastructure, schema, code, document, spec, plan, requirements, runbook, api-docs, jupyter
