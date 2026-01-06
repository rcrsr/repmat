# Workflow Policy

Standards for the artifact-centric workflow framework: artifacts, author/reviewer pairs, commands, pipelines, and policies.

## Table of Contents

- [Core Model](#wft1-core-model)
  - [Artifact-Centric Principle](#wft11-artifact-centric-principle)
  - [The Artifact Blueprint](#wft12-the-artifact-blueprint)
  - [Layers](#wft13-layers)
- [Artifact Standards](#wft2-artifact-standards)
  - [Artifact Registry](#wft21-artifact-registry)
  - [Artifact Typing](#wft22-artifact-typing)
  - [Extensibility](#wft23-extensibility)
- [Subagent Standards](#wft3-subagent-standards)
  - [Author/Reviewer Pattern](#wft31-authorreviewer-pattern)
  - [Semantic Naming](#wft32-semantic-naming)
  - [Support Subagents](#wft33-support-subagents)
  - [Subagent Structure](#wft34-subagent-structure)
  - [Author Output](#wft35-author-output)
  - [Reviewer Output](#wft36-reviewer-output)
- [Command Standards](#wft4-command-standards)
  - [Commands as Transformations](#wft41-commands-as-transformations)
  - [Command Structure](#wft42-command-structure)
  - [Orchestration Patterns](#wft43-orchestration-patterns)
  - [Failure Handling](#wft44-failure-handling)
- [Pipeline Standards](#wft5-pipeline-standards)
  - [Pipeline Purpose](#wft51-pipeline-purpose)
  - [Pipeline Structure](#wft52-pipeline-structure)
  - [Workflow State](#wft53-workflow-state)
- [Policy Standards](#wft6-policy-standards)
  - [Policy Structure](#wft61-policy-structure)
  - [Completeness Rule](#wft62-completeness-rule)
- [Infrastructure](#wft7-infrastructure)
  - [Registry File](#wft71-registry-file)
  - [Policy Fetch Hook](#wft72-policy-fetch-hook)
- [Quality Criteria](#wft8-quality-criteria)
  - [Artifact Quality](#wft81-artifact-quality)
  - [Subagent Quality](#wft82-subagent-quality)
  - [Command Quality](#wft83-command-quality)
  - [Anti-Patterns](#wft84-anti-patterns)

## {§WFT.1} Core Model

### {§WFT.1.1} Artifact-Centric Principle

Everything flows from artifact types. The organizing principle is:

**Artifacts are foundational.** Policies govern them, subagents transform them, commands orchestrate transformations, pipelines sequence commands.

| Layer | Purpose |
|-------|---------|
| Layer 5: Pipelines | Orchestrate commands across phases |
| Layer 4: Commands | Transform artifacts via author/reviewer pairs |
| Layer 3: Subagents | Author/reviewer pairs bound to artifact types |
| Layer 2: Policies | Govern artifact structure and author behavior |
| Layer 1: Artifacts | Typed work products—the foundation |

### {§WFT.1.2} The Artifact Blueprint

Each artifact type is defined by exactly one blueprint:

```
(Artifact Type, Author, Reviewer, Policies)
```

| Component | Description |
|-----------|-------------|
| Artifact Type | Name identifying the work product |
| Author | Subagent that produces/modifies this artifact |
| Reviewer | Subagent that validates against policies |
| Policies | Files governing structure and behavior |

### {§WFT.1.3} Layers

**Layer 1 (Artifacts):** Typed work products persisted as files.

**Layer 2 (Policies):** Governance rules for artifact structure and author behavior.

**Layer 3 (Subagents):** Author/reviewer pairs. Each artifact type has exactly one author and one reviewer.

**Layer 4 (Commands):** User-invokable workflows that transform input artifacts to output artifacts by orchestrating author/reviewer pairs.

**Layer 5 (Pipelines):** Multi-phase workflows that sequence commands. Human-in-the-loop; pipelines describe but do not auto-execute.

## {§WFT.2} Artifact Standards

### {§WFT.2.1} Artifact Registry

The artifact registry defines the system. Each row specifies an artifact type with its author/reviewer pair and governing policies.

| Field | Required | Description |
|-------|----------|-------------|
| Artifact Type | Yes | Unique name (e.g., `backend-spec`, `plan`) |
| File Pattern | Yes | Glob pattern for artifact locations |
| Author | Yes | Subagent that produces this artifact |
| Reviewer | Yes | Subagent that validates this artifact |
| Policies | Yes | Governing policy file(s) |

**Example registry:**

| Artifact Type | File Pattern | Author | Reviewer | Policies |
|---------------|--------------|---------|----------|----------|
| backend-spec | `docs/specs/backend-*.md` | backend-architect | backend-spec-reviewer | `backend.md`, `spec.md` |
| backend-code | `apps/api/**/*.py` | backend-engineer | backend-code-reviewer | `backend.md`, `python.md` |
| plan | `docs/plans/*.md` | plan-editor | plan-reviewer | `plan.md` |

### {§WFT.2.2} Artifact Typing

Each artifact has exactly one governing type. No mixing.

| Rule | Example |
|------|---------|
| Single type per artifact | A plan with code snippets is still governed by `plan.md` |
| Type determines validation | The policy for the artifact type defines what's valid |
| Content vs type | Embedded snippets are content; the artifact type governs structure |

### {§WFT.2.3} Extensibility

New artifact types require:

1. A governing policy (existing or new)
2. A file pattern
3. A author subagent
4. A reviewer subagent
5. Commands that produce/consume the artifact

Artifact types are defined at design-time, not created ad-hoc during workflow execution.

## {§WFT.3} Subagent Standards

### {§WFT.3.1} Author/Reviewer Pattern

Every artifact type has exactly two subagents:

| Role | Purpose | Output |
|------|---------|--------|
| Author | Produces or modifies artifacts | Artifact + summary |
| Reviewer | Validates artifacts against policies | Verdict + findings |

This pattern is universal. No artifact type has more or fewer than one author and one reviewer.

### {§WFT.3.2} Semantic Naming

Authors use semantic names based on what they create:

| Semantic Name | Creates | Example |
|---------------|---------|---------|
| architect | specs/designs | `backend-architect` creates `backend-spec` |
| engineer | code | `backend-engineer` creates `*.py` |
| editor | documents | `plan-editor` creates `plan.md` |

Reviewers follow the pattern `[artifact-type]-reviewer`:

| Artifact Type | Reviewer |
|---------------|----------|
| backend-spec | backend-spec-reviewer |
| backend-code | backend-code-reviewer |
| plan | plan-reviewer |

### {§WFT.3.3} Support Subagents

Some subagents gather context but don't produce persistent artifacts:

| Type | Purpose | Example |
|------|---------|---------|
| Explorer | Analyze codebase structure and patterns | codebase-explorer |
| Investigator | Diagnose failures, find root causes | root-cause-investigator |

Support subagents feed context into authors. They are not part of author/reviewer pairs.

### {§WFT.3.4} Subagent Structure

```yaml
---
name: [artifact-type]-[role]
description: [Purpose]. Use PROACTIVELY when [trigger].
tools: [tool list]
---

## Required Policies

["policy1.md", "policy2.md"]

## Workflow

1. [Step 1]
2. [Step 2]
3. [Step 3]

## Output Format

[Fenced example of expected output]

## Success Criteria

- [Criterion 1]
- [Criterion 2]
```

| Field | Required | Rules |
|-------|----------|-------|
| name | Yes | `[domain]-[role]` format |
| description | Yes | Include "PROACTIVELY" for auto-invoke |
| tools | No | Omit for full access; list to restrict |
| Required Policies | Yes | JSON array of policy files |

**Size:** 100-200 lines target. Over 250 indicates over-specification.

### {§WFT.3.5} Author Output

Authors report what was done:

```markdown
**Summary:** [What was created/modified]
**Files:** [List of files changed]
**Policy Compliance:** [Policies followed]
```

### {§WFT.3.6} Reviewer Output

Reviewers return verdicts:

```markdown
**Status:** PASS | FAIL
**Summary:** [1-2 sentence overview]

## Findings

### [Category]: [Issue]
**Policy:** [policy.md section]
**Location:** path/to/file:line
**Fix:** [Suggested correction]
```

## {§WFT.4} Command Standards

### {§WFT.4.1} Commands as Transformations

Commands transform artifacts by orchestrating author/reviewer pairs.

| Field | Description |
|-------|-------------|
| Input Artifact | What the command consumes |
| Output Artifact | What the command produces |
| Uses | Which authors and reviewers |

**Example:**

| Command | Input | Output | Uses |
|---------|-------|--------|------|
| /create-spec | requirements | `*-spec.md` | architects, spec-reviewers |
| /implement-plan | plan | `*.py`, `*.tsx` | engineers, code-reviewers |
| /review-code | `*.py`, `*.tsx` | verdict | code-reviewers |

### {§WFT.4.2} Command Structure

```yaml
---
description: [Brief purpose]
argument-hint: [argument syntax]
---

## Step 1: [Action]
[Instructions or Task calls]

## Step 2: [Action]
[Instructions or Task calls]

## Step N: Report
[Summary and next command recommendation]

## Important Rules
[Constraints]
```

| Element | Required | Purpose |
|---------|----------|---------|
| description | Yes | User-facing summary (under 80 chars) |
| argument-hint | No | Argument syntax for command picker |
| Numbered steps | Yes | Sequential with action verb headers |
| Important Rules | No | Constraints and requirements |

### {§WFT.4.3} Orchestration Patterns

**Parallel authors** (single message, multiple Task calls):

```markdown
Task(@backend-architect, "Create spec for...")
Task(@frontend-architect, "Create spec for...")
```

**Author → Reviewer flow:**

```markdown
## Step 2: Create
Task(@backend-engineer, "Implement...")

## Step 3: Review
Task(@backend-code-reviewer, "Review...")
```

**Support → Author flow:**

```markdown
## Step 1: Gather Context
Task(@codebase-explorer, "Find patterns...")

## Step 2: Create
Task(@backend-architect, "Using patterns, create...")
```

### {§WFT.4.4} Failure Handling

| Failure | Strategy |
|---------|----------|
| Reviewer rejects | Loop back to author with feedback (max 2-3 iterations) |
| Author blocked | Stop, report to user |
| Prerequisites missing | Recommend prior command |

## {§WFT.5} Pipeline Standards

### {§WFT.5.1} Pipeline Purpose

Pipelines **describe** workflows but do not **execute** them. Human invokes commands; pipelines document the sequence.

```
Pipeline = Documentation of workflow structure
Human    = Invokes commands, makes decisions
Commands = Execute with author/reviewer pairs
```

### {§WFT.5.2} Pipeline Structure

```markdown
# [Pipeline Name]

[Description]

## Artifacts

| Artifact | Policy | Location |
|----------|--------|----------|
| requirements | requirements.md | docs/requirements/*.md |
| spec | spec.md | docs/specs/*.md |

## Phases

### 1. [Phase Name]
- **Entry**: [input artifact]
- **Commands**: [command list]
- **Review loop**: [review] ↔ [improve] (max N iterations)
- **Exit**: [condition]
- **Output**: [artifact]

## Transitions

| From | To | Condition |
|------|----|-----------|
| Phase 1 | Phase 2 | Auto |
| Phase 2 | Phase 3 | Manual |
```

### {§WFT.5.3} Workflow State

State is embedded in artifacts—no separate state store.

| State Type | Location | Example |
|------------|----------|---------|
| Document status | `**Status:**` field | Draft → Approved |
| Task completion | Checkbox notation | `[ ]` → `[x]` |
| Review verdict | Output section | `**Status:** PASS` |

## {§WFT.6} Policy Standards

### {§WFT.6.1} Policy Structure

Every policy requires (per §META):

1. **Title** — `# Policy Name`
2. **Introduction** — One-sentence description
3. **Table of Contents** — Linked sections
4. **Sections** — Content with `{§PREFIX.X}` notation
5. **End marker** — `{§END}`

```markdown
# Domain Policy

Brief description.

## Table of Contents

- [Section One](#prefix1-section-one)
- [Section Two](#prefix2-section-two)

## {§PREFIX.1} Section One

Content.

## {§PREFIX.2} Section Two

Content.

{§END}
```

### {§WFT.6.2} Completeness Rule

Policies are self-contained and internally complete.

| Do | Avoid |
|----|-------|
| Include all necessary context | Require external reading |
| Define terms on first use | Assume prior knowledge |
| Provide actionable rules | Abstract principles only |
| Show examples (under 10 LOC) | Full implementations |

## {§WFT.7} Infrastructure

### {§WFT.7.1} Registry File

The artifact registry is stored as YAML. Plugins and projects each maintain their own registry; both are loaded and merged at runtime.

**Locations:**
```
Plugin:  ${CLAUDE_PLUGIN_ROOT}/registry.yaml
Project: $CLAUDE_PROJECT_DIR/.claude/registry.yaml
```

**Schema:**

```yaml
artifacts:
  <artifact-type>:
    patterns: [<glob>, ...]     # Required: file patterns (array)
    author: <subagent-name>    # Required: author subagent
    reviewer: <subagent-name>   # Required: reviewer subagent
    policies: [<file>, ...]     # Required: governing policies
    auto_review: <boolean>      # Optional: trigger reviewer after author (default: false)
    meta_type: <type>           # Optional: stateless | status | tasklist (default: stateless)
    states: [<state>, ...]      # Optional: valid states (for meta_type: status)
    tasklist_type: <type>       # Optional: single | multiphase (for meta_type: tasklist)
```

**Field definitions:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| patterns | array | Yes | Glob patterns matching artifact files |
| author | string | Yes | Subagent that produces this artifact |
| reviewer | string | Yes | Subagent that validates this artifact |
| policies | array | Yes | Policy files governing this artifact |
| auto_review | boolean | No | If true, reviewer runs automatically after author completes |
| meta_type | string | No | Artifact state model: `stateless`, `status`, or `tasklist` |
| states | array | No | Valid status values (required when meta_type: status) |
| tasklist_type | string | No | Task structure: `single` or `multiphase` (required when meta_type: tasklist) |

**Meta-types:**

| Meta-Type | State Mechanism | Use Case |
|-----------|-----------------|----------|
| stateless | None | Code files—no embedded state |
| status | `**Status:** <state>` field | Specs, requirements—lifecycle tracking |
| tasklist | `[ ]`/`[x]` checkboxes | Plans—progress tracking |

**Tasklist structures:**

| Type | Structure | Example |
|------|-----------|---------|
| single | Flat task list | `[ ] Task 1`, `[x] Task 2` |
| multiphase | Phases containing tasks | `## Phase 1` with `[ ] Task 1.1` |

**Merge behavior:** Project registry extends and overrides plugin registry. Same artifact type in project replaces plugin definition.

### {§WFT.7.2} Policy Fetch Hook

**Required.** Makes `## Required Policies` functional.

```
Command → Task(subagent) → policy-fetch hook → inject policies → execute
```

Without this hook, subagents cannot access their required policies.

## {§WFT.8} Quality Criteria

### {§WFT.8.1} Artifact Quality

| Criterion | Requirement |
|-----------|-------------|
| Single type | One governing policy per artifact |
| File pattern | Defined location in registry |
| Author/reviewer pair | Both defined in registry |

### {§WFT.8.2} Subagent Quality

| Criterion | Requirement |
|-----------|-------------|
| Single responsibility | One artifact type per subagent |
| Proper naming | Author: semantic name; Reviewer: `[type]-reviewer` |
| Output format | Explicit structure with fenced example |
| Required Policies | Section present with JSON array |
| Size | 100-200 lines target, 250 max |

### {§WFT.8.3} Command Quality

| Criterion | Requirement |
|-----------|-------------|
| Input/output defined | Clear artifact transformation |
| Uses authors/reviewers | From artifact registry |
| Numbered steps | Sequential with action verbs |
| Failure handling | Explicit strategy |

### {§WFT.8.4} Anti-Patterns

| Anti-Pattern | Problem | Resolution |
|--------------|---------|------------|
| Artifact without pair | Missing author or reviewer | Define both in registry |
| Author produces multiple types | Violates single responsibility | One author per artifact type |
| Reviewer that modifies | Role confusion | Reviewers only validate |
| Command without transformation | No clear input→output | Define artifact flow |
| Pipeline that auto-executes | Removes human control | Document-only; human invokes |
| Subagent over 250 lines | Over-specification | Split or reduce |

{§END}
