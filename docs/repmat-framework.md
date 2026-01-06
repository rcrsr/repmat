# Repmat Framework

**Status:** Draft
**Purpose:** A construction kit for building Claude Code workflows from composable primitives

---

## Overview

Repmat provides a layered architecture for building custom Claude Code workflows. The organizing principle is **artifact-centric**: everything flows from artifact types.

### Blueprints

A **blueprint** defines everything needed to work with an artifact type:

```
(Artifact Type, Author, Reviewer, Policies)
```

Think about how teams work: an engineer writes code, a reviewer checks it, and both follow the team's style guide. Repmat captures this pattern as a blueprint—a definition that binds an artifact type to who creates it, who reviews it, and what rules govern it.

### Commands

Use these commands to create blueprints:

| Command | Scenario | Description |
|---------|----------|-------------|
| `/repmat:design-blueprint <name>` | Greenfield | Design blueprint from scratch |
| `/repmat:discover-blueprint <name>` | Green/Brown | Infer blueprint from existing files |
| `/repmat:modify-blueprint <type>` | Brownfield | Modify existing blueprint |
| `/repmat:implement-blueprint <path>` | All | Implement blueprint (creates agents + registry) |

### Example Blueprints

| Artifact Type | Author | Reviewer | Policies |
|---------------|---------|----------|----------|
| backend-spec | backend-architect | backend-spec-reviewer | `backend.md`, `spec.md` |
| frontend-spec | frontend-architect | frontend-spec-reviewer | `frontend.md`, `spec.md` |
| backend-code | backend-engineer | backend-code-reviewer | `backend.md`, `python.md` |
| frontend-code | frontend-engineer | frontend-code-reviewer | `frontend.md`, `typescript.md` |
| infra-code | infra-engineer | infra-code-reviewer | `cdk.md`, `typescript.md` |
| plan | plan-editor | plan-reviewer | `plan.md` |
| requirements | requirements-editor | requirements-reviewer | `requirements.md` |

Each row is a blueprint. The registry defines the entire system. Subagents come in **author/reviewer pairs** bound to artifact types. Commands orchestrate these pairs to transform artifacts.

---

## Architectural Layers

```
┌─────────────────────────────────────────────────────────────┐
│  Layer 5: Pipelines (Top-Down Entry Point)                  │
│  Orchestrate multiple commands across phases                │
├─────────────────────────────────────────────────────────────┤
│  Layer 4: Commands                                          │
│  Transform artifacts by orchestrating author/reviewer pairs│
├─────────────────────────────────────────────────────────────┤
│  Layer 3: Subagents                                         │
│  Author/reviewer pairs bound to artifact types             │
├─────────────────────────────────────────────────────────────┤
│  Layer 2: Policies                                          │
│  Govern artifact structure and author behavior             │
├─────────────────────────────────────────────────────────────┤
│  Layer 1: Artifacts (Bottom-Up Foundation)                  │
│  Typed work products—the foundation everything operates on  │
└─────────────────────────────────────────────────────────────┘
```

### Two Entry Points

**Bottom-Up (Minimum Viable Workflow):**

```
1 Artifact Type + 1 Author + 1 Reviewer + Policies + 1 Command = Workflow
```

Start with a single artifact type, define its author/reviewer pair.

**Top-Down (Pipeline Manifest):**

```
Pipeline → Commands → Author/Reviewer Pairs → Policies → Artifacts
```

Define the full process, then implement the artifact registry.

---

## Layer 1: Artifacts

**Purpose:** Typed work products—the foundation everything operates on.

**Characteristics:**

- Persist as files in the repository
- Have a defined type with governing policies
- Each type has exactly one author/reviewer pair
- Are inputs to and outputs from commands

### The Artifact Registry

The artifact registry is the system definition. Each row is a blueprint—defining an artifact type with its author/reviewer pair and governing policies:

| Artifact Type | File Pattern | Author | Reviewer | Policies |
|---------------|--------------|---------|----------|----------|
| backend-spec | `docs/specs/backend-*.md` | backend-architect | backend-spec-reviewer | `backend.md`, `spec.md` |
| frontend-spec | `docs/specs/frontend-*.md` | frontend-architect | frontend-spec-reviewer | `frontend.md`, `spec.md` |
| infra-spec | `docs/specs/infra-*.md` | infra-architect | infra-spec-reviewer | `cdk.md`, `spec.md` |
| backend-code | `apps/api/**/*.py` | backend-engineer | backend-code-reviewer | `backend.md`, `python.md` |
| frontend-code | `apps/web/**/*.tsx` | frontend-engineer | frontend-code-reviewer | `frontend.md`, `typescript.md` |
| infra-code | `infra/**/*.ts` | infra-engineer | infra-code-reviewer | `cdk.md`, `typescript.md` |
| plan | `docs/plans/*.md` | plan-editor | plan-reviewer | `plan.md` |
| requirements | `docs/requirements/*.md` | requirements-editor | requirements-reviewer | `requirements.md` |
| brief | `docs/briefs/*.md` | brief-editor | brief-reviewer | `brief.md` |

### Artifact Flow

```
[Input Artifact] → Command → Author(s) → [Output Artifact] → Reviewer(s) → Verdict
                                 ↑                                  ↑
                           Policies govern                   Policies validate
```

### Single Type Per Artifact

Artifacts have exactly one governing type—no mixing. A plan with code snippets is still a plan governed by §PLAN; the snippets are content, not type.

### Extensibility

New blueprints require:
1. A governing policy (existing or new)
2. A file pattern
3. A author subagent
4. A reviewer subagent
5. Commands that produce/consume the artifact

Use `/wft:design-blueprint` or `/wft:discover-blueprint` to create new blueprints.

---

## Layer 2: Policies

**Purpose:** Codify domain knowledge, constraints, and quality standards.

**Characteristics:**

- Declarative, not procedural
- Version-controlled organizational knowledge
- Referenced by subagents via `## Required Policies` sections
- Auto-fetched by hooks when subagents are invoked

**Policy Categories:**

| Category         | Examples                   | Governs                       |
| ---------------- | -------------------------- | ----------------------------- |
| Coding Standards | §BASIC, §PY, §TS           | Language-specific patterns    |
| Architecture     | §BE, §FE, §CDK             | Domain architecture decisions |
| Document Formats | §REQ, §BRIEF, §SPEC, §PLAN | Artifact structure rules      |
| Governance       | §META, §MONO, §AGENT       | System-level constraints      |

**Policy Structure (per §META):**

Minimum viable policy requires:
1. Title - `# Domain Policies`
2. Introduction - One-sentence description
3. Table of Contents - Linked section hierarchy
4. Sections - Content with `{§PREFIX.X}` notation
5. End marker - `{§END}` at document end

**Policy Completeness:**

A valid policy must be **internally complete**. It should be usable on its own without requiring another policy to function.

- §PY is complete for Python guidance
- §BE is complete for backend architecture guidance
- Cross-references to other policies are informational, not structural dependencies

**Policy References:**

Policies may reference other policies for context, but references don't create inheritance or composition:

```markdown
Apply YAGNI principles (§BASIC.3) when designing agents.
```

| Pattern | Example | Meaning |
|---------|---------|---------|
| Same-policy ref | §PY.6.1 | Section within current policy |
| Cross-policy ref | §BASIC.3 | Informational reference to another policy |
| Policy-level ref | §BE | Entire policy document |

**Key distinction:**
- Policies are atomic and self-contained
- Artifact types compose one or more policies together
- References are informational, not structural

---

## Layer 3: Subagents

**Purpose:** Execute atomic units of work with policy awareness. Subagents come in **author/reviewer pairs** bound to artifact types.

### The Author/Reviewer Pattern

Every artifact type has exactly two subagents:

- **Author**: Produces or modifies artifacts of that type
- **Reviewer**: Validates artifacts against governing policies

This pattern is universal. The artifact registry (Layer 1) defines which author and reviewer handle each artifact type.

### Semantic Naming

Authors have semantic names based on what they create:

| Semantic Name | Creates | Example |
|---------------|---------|---------|
| architect | specs/designs | backend-architect → backend-spec |
| engineer | code | backend-engineer → backend-code |
| editor | documents | plan-editor → plan |

Reviewers follow the pattern `[artifact-type]-reviewer`:

| Artifact Type | Reviewer |
|---------------|----------|
| backend-spec | backend-spec-reviewer |
| backend-code | backend-code-reviewer |
| plan | plan-reviewer |

### Support Subagents

Some subagents don't produce persistent artifacts—they gather context for authors:

| Subagent | Purpose | Output |
|----------|---------|--------|
| codebase-explorer | Analyze structure, patterns, impact | Context for architects |
| root-cause-investigator | Diagnose failures | Hypothesis for engineers |

These are **support subagents**, not part of author/reviewer pairs.

### Subagent Structure

```yaml
---
name: backend-engineer
description: Creates and modifies backend Python code. Use PROACTIVELY when implementing backend tasks.
tools: Read, Write, Edit, Grep, Bash
---

## Required Policies

["§BE", "§PY"]

## Workflow

1. Analyze task requirements
2. Implement changes
3. Verify against policies
4. Report completion

## Output Format

**Summary:** [What was done]
**Files Changed:** [List]
**Policy Compliance:** [Verification]

## Success Criteria

- Code compiles/passes linting
- Changes match task requirements
- No policy violations
```

### Model Selection

| Model | Use Case | Example |
|-------|----------|---------|
| haiku | Fast validation, rule-based checks | reviewers |
| sonnet | Balanced (default) | engineers, architects |
| opus | Complex reasoning | security review, difficult design |

### Author Output vs Reviewer Output

**Authors** produce artifacts and report what was done:

```markdown
**Summary:** Implemented user authentication endpoint
**Files Changed:** apps/api/auth.py, apps/api/routes.py
**Policy Compliance:** §BE.3 (auth patterns), §PY.4 (error handling)
```

**Reviewers** validate and return verdicts:

```markdown
**Status:** PASS | FAIL
**Summary:** [1-2 sentence overview]

## Findings
### [Category]: [Issue]
**Policy:** §SECTION
**Location:** path/to/file.py:line
**Fix:** [Suggested correction]
```

---

## Layer 4: Commands

**Purpose:** Transform artifacts by orchestrating author/reviewer pairs.

**Characteristics:**

- User-invokable via `/command-name`
- Takes input artifact(s), produces output artifact(s)
- Orchestrates authors and reviewers from the artifact registry
- Recommends next command in workflow

### Commands as Artifact Transformations

| Command | Input Artifact | Output Artifact | Uses |
|---------|---------------|-----------------|------|
| /create-spec | requirements | backend-spec, frontend-spec | architects |
| /review-spec | `*-spec.md` | verdict | spec-reviewers |
| /create-plan | `*-spec.md` | plan | plan-editor |
| /review-plan | plan | verdict | plan-reviewer |
| /implement-plan | plan | `*.py`, `*.tsx`, `*.ts` | engineers |
| /review-code | `*.py`, `*.tsx`, `*.ts` | verdict | code-reviewers |

### Command Structure

```yaml
---
description: Create specification from requirements document
argument-hint: "docs/requirements/my-feature.md"
---

## Step 1: Gather Context
Task(@codebase-explorer, "Analyze patterns relevant to $1...")

## Step 2: Create Artifacts
Task(@backend-architect, "Create backend spec based on $1...")
Task(@frontend-architect, "Create frontend spec based on $1...")

## Step 3: Review
Task(@backend-spec-reviewer, "Review the backend spec...")
Task(@frontend-spec-reviewer, "Review the frontend spec...")

## Step 4: Report Completion
Summarize outputs. Recommend: `/review-spec [output paths]`

## Important Rules
- Never skip review step
- Max 2 revision loops on review failure
```

### Orchestration Patterns

**Parallel authors** (single message, multiple Task calls):
```markdown
Task(@backend-architect, "...")
Task(@frontend-architect, "...")
```

**Author → Reviewer flow**:
```markdown
## Step 2: Create
Task(@backend-engineer, "Implement feature...")

## Step 3: Review
Task(@backend-code-reviewer, "Review the implementation...")
```

**Support → Author flow**:
```markdown
## Step 1: Gather Context
Task(@codebase-explorer, "Find existing patterns...")

## Step 2: Create
Task(@backend-architect, "Using the patterns found, create spec...")
```

### Failure Handling

| Failure | Strategy |
|---------|----------|
| Reviewer rejects | Loop back to author with feedback (max 2-3 iterations) |
| Author blocked | Stop, report to user |
| Prerequisites missing | Recommend prior command |

---

## Layer 5: Pipelines

**Purpose:** Define how all pieces hang together. The top-down entry point for workflow design.

**Key Principle: Human-in-the-Loop**

Pipeline manifests **describe** the system but do not **operate** it. The human always invokes commands—this is by design, not a limitation.

```
Pipeline = Documentation of workflow structure
Human    = Invokes commands, makes decisions at transitions
Commands = Execute with subagent delegation
```

This keeps humans in control of pace, can skip phases, handle exceptions, and make judgment calls that automation cannot.

**Characteristics:**

- Declares the complete artifact flow graph
- Defines phase transitions and quality gates
- Specifies iteration loops and exit conditions
- References (not duplicates) commands, subagents, and policies
- Enables progress tracking across the full workflow
- **Does NOT auto-execute**—human invokes each command

### Pipeline Manifest

A pipeline manifest file describes a complete workflow in Markdown:

```markdown
# Feature Development Workflow

End-to-end feature development from requirements to implementation.

## Artifacts

| Artifact | Policy | Location |
|----------|--------|----------|
| requirements | §REQ | docs/requirements/*.md |
| specification | §SPEC | docs/specs/*.md |
| plan | §PLAN | docs/plans/*.md |
| backend-code | §BE, §PY | apps/api/**/*.py |
| frontend-code | §FE, §TS | apps/web/**/*.tsx |

## Phases

### 1. Requirements
- **Entry**: requirements document
- **Commands**: `/review-requirements`
- **Exit**: Verdict = READY

### 2. Specification
- **Entry**: requirements (from phase 1)
- **Commands**: `/create-spec`
- **Review loop**: `/review-spec` ↔ `/improve-spec` (max 3 iterations)
- **Exit**: Verdict = APPROVED or APPROVED WITH NOTES
- **Output**: specification

### 3. Planning
- **Entry**: specification (from phase 2)
- **Commands**: `/create-plan`
- **Review loop**: `/review-plan` ↔ `/improve-plan` (max 2 iterations)
- **Exit**: Verdict = COMPLETE or ADEQUATE
- **Output**: plan

### 4. Implementation
- **Entry**: plan (from phase 3)
- **Commands**: `/implement-plan`
- **Mode**: Per-phase (pause after each plan phase for user confirmation)
- **Output**: backend-code, frontend-code

### 5. Validation
- **Entry**: code changes (from phase 4)
- **Commands**: `/review-implementation`, `/pre-commit`
- **Exit**: All pass

## Transitions

| From | To | Condition |
|------|----|-----------|
| Requirements | Specification | Auto |
| Specification | Planning | Auto |
| Planning | Implementation | Manual (user confirms) |
| Implementation | Validation | Auto |
```

### Workflow State

State is embedded in artifacts—no separate state store:

| State Type | Location | Example |
|------------|----------|---------|
| Document status | `**Status:**` field | `**Status:** Draft` → `**Status:** Approved` |
| Task completion | Checkbox notation | `[ ]` → `[x]` |
| Review verdict | Output section | `**Verdict:** APPROVED` |
| Phase progress | Plan file | Count of `[x]` vs `[ ]` tasks |

Commands read state by parsing artifacts:
```markdown
## Step 1: Determine Starting Point

Parse the plan file to find:
- Completed tasks: lines matching `[x]` or `[X]`
- Incomplete tasks: lines matching `[ ]`
- Current phase: first phase with incomplete tasks
```

This enables resume across sessions without external state management.

**Pipeline Examples:**

**Feature Development:**

```
Requirements → /review-requirements
           → /create-spec
           → /review-spec ←→ /improve-spec (loop)
           → /create-plan
           → /review-plan ←→ /improve-plan (loop)
           → /implement-plan (per phase)
           → /review-implementation
           → /pre-commit
```

**Tech Debt:**

```
Brief → /review-brief
     → /create-spec-from-brief
     → /review-spec ←→ /improve-spec (loop)
     → /create-plan
     → /review-plan ←→ /improve-plan (loop)
     → /implement-plan
     → /pre-commit
```

**Manifest Structure:**

```markdown
# [Workflow Name]

[Description]

## Artifacts
[Table: Artifact | Policy | Location]

## Phases
### N. [Phase Name]
- **Entry**: [artifact from prior phase]
- **Commands**: [command list]
- **Review loop**: [review] ↔ [improve] (max N iterations)  # optional
- **Exit**: [condition]
- **Output**: [artifact]  # optional

## Transitions
[Table: From | To | Condition]
```

**Visualization:**

Workflow diagrams use ASCII art - no external tooling required:

```
Requirements ──► /review-requirements
                        │
                        ▼
              /create-spec
                        │
                        ▼
              ┌─────────────────┐
              │  /review-spec   │◄──┐
              └────────┬────────┘   │
                       │            │
            APPROVED?  │  NO        │
                       ▼            │
              ┌─────────────────┐   │
              │  /improve-spec  │───┘
              └─────────────────┘
                       │ YES
                       ▼
              /create-plan
                       │
                       ▼
                     ...
```

---

## Minimum Viable Workflow (Bottom-Up)

The simplest functional workflow requires one blueprint:

```
1 Blueprint = 1 Artifact Type + 1 Author + 1 Reviewer + Policies + 1 Command
```

### Example: Script Blueprint

**1. Define the blueprint:**

| Artifact Type | File Pattern | Author | Reviewer | Policies |
|---------------|--------------|---------|----------|----------|
| script | `scripts/*.py` | script-engineer | script-reviewer | §PY |

**2. Create the author (`agents/script-engineer.md`):**

```markdown
---
name: script-engineer
description: Creates and modifies Python scripts
tools: Read, Write, Edit, Grep, Bash
---

## Required Policies
["§PY"]

## Workflow
1. Analyze requirements
2. Implement script
3. Test execution
4. Report completion

## Output Format
**Summary:** [What was created/changed]
**Files:** [List of files]
```

**3. Create the reviewer (`agents/script-reviewer.md`):**

```markdown
---
name: script-reviewer
description: Reviews Python scripts for quality
tools: Read, Grep, Glob
model: haiku
---

## Required Policies
["§PY"]

## Workflow
1. Read script files
2. Check against §PY standards
3. Return verdict

## Output Format
**Status:** PASS | FAIL
**Findings:** [List of issues if any]
```

**4. Create the command (`commands/create-script.md`):**

```markdown
---
description: Create a Python script
argument-hint: "description of what the script should do"
---

## Step 1: Create
Task(@script-engineer, "Create a script that: $1")

## Step 2: Review
Task(@script-reviewer, "Review the script created above")

## Step 3: Report
Summarize. If review failed, loop back to Step 1 (max 2 iterations).
```

**Result:** A working `/create-script` command with author/reviewer flow.

### Scaling Up

| Add | Result |
|-----|--------|
| More artifact types | Broader coverage (specs, plans, configs) |
| More policies | Richer rules per artifact type |
| More commands | Full workflows (/improve-script, /test-script) |
| Pipeline manifest | Multi-phase orchestration |

---

## Cross-Cutting Concerns

### Hooks

**Purpose:** Automated behaviors triggered by tool usage.

**Hook Types:**

| Type | Trigger | Example |
|------|---------|---------|
| `PreToolUse` | Before tool execution | policy-fetch |
| `PostToolUse` | After tool execution | code-reviewer |
| `Lifecycle` | Session events | invocation capture |

### Required Hook: policy-fetch

The **policy-fetch hook** is required infrastructure. It makes `## Required Policies` declarations functional.

**How it works:**

1. Command invokes a subagent via `Task` tool
2. `PreToolUse` hook intercepts the `Task` call
3. Hook reads subagent definition, finds `## Required Policies`
4. Hook fetches policy content and injects into subagent context
5. Subagent executes with policy knowledge

```
Command                    policy-fetch hook              Subagent
   │                              │                          │
   │──── Task(backend-engineer) ──►│                          │
   │                              │                          │
   │                              │── Read agent definition  │
   │                              │── Find: §BE, §PY         │
   │                              │── Fetch policy content   │
   │                              │── Inject into context ───►│
   │                              │                          │
   │◄─────────────────────────────────── Result ─────────────│
```

**Without this hook:** Subagents would reference policies they cannot see. The `## Required Policies` section would be documentation-only.

**Configuration (`.claude/settings.json`):**

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Task",
        "command": "policy-fetch"
      }
    ]
  }
}
```

### Optional Hooks

| Hook | Purpose | When to Use |
|------|---------|-------------|
| code-reviewer | Auto-trigger review after engineer tasks | Quality enforcement |
| quality-gate | Lint/format/typecheck on file changes | Pre-commit validation |
| invocation-capture | Save session transcripts | Debugging, post-mortems |

### Context Rules

**Purpose:** Load context based on file patterns.

**Pattern:**

- Rules in `.claude/rules/` directory
- Triggered by file path patterns
- Inject domain-specific guidance

---

## Design Kit Components

### Bottom-Up Starter Kit

For users who want to start small and grow organically:

| Component | Template            | Required | Purpose                           |
| --------- | ------------------- | -------- | --------------------------------- |
| Hook      | `policy-fetch`      | Yes      | Makes `## Required Policies` work |
| Policy    | `policy-minimal.md` | Yes      | Single-concern policy with rules  |
| Subagent  | `subagent-task.md`  | Yes      | Single-task agent with policy ref |
| Command   | `command-simple.md` | Yes      | One-subagent command              |

**Getting Started:**

1. Configure the policy-fetch hook (required infrastructure)
2. Define what rules govern your task (→ policy)
3. Define who does the work (→ subagent)
4. Define how users invoke it (→ command)

### Top-Down Starter Kit

For users who want to design a complete workflow upfront:

| Component         | Template                 | Purpose                             |
| ----------------- | ------------------------ | ----------------------------------- |
| Manifest          | `workflow-manifest.md`   | Full workflow definition (Markdown) |
| Artifact Registry | `artifacts.md`           | Declare artifact types and policies |
| Phase Template    | `phase-review-loop.md`   | Common review/improve pattern       |

**Getting Started:**

1. Map your artifact flow (requirements → spec → code)
2. Define phases and their entry/exit conditions
3. Stub out commands and subagents referenced by manifest
4. Implement each stub bottom-up

### Template Library

**Policy Templates:**

- `policy-coding-standards.md` - Language-specific rules
- `policy-architecture.md` - Architectural constraints
- `policy-document-format.md` - Artifact structure rules
- `policy-quality-gate.md` - Validation criteria

**Subagent Templates (by role):**

- `subagent-architect.md` - Design decisions, pattern guidance
- `subagent-engineer.md` - Code implementation
- `subagent-reviewer.md` - Quality validation
- `subagent-editor.md` - Document modification
- `subagent-explorer.md` - Codebase analysis

**Command Templates (by pattern):**

- `command-create.md` - Transform input artifact to output artifact
- `command-review.md` - Validate artifact, return verdict
- `command-improve.md` - Apply feedback to artifact
- `command-implement.md` - Generate code from plan
- `command-fix.md` - Investigate and repair
- `command-validate.md` - Run quality gates

**Workflow Templates:**

- `workflow-linear.md` - Sequential phases, no loops
- `workflow-iterative.md` - Review/improve loops
- `workflow-parallel.md` - Concurrent workstreams

### Hook Templates

- `hook-policy-fetch.ts` - Auto-fetch policies for subagents
- `hook-code-review.ts` - Trigger review after engineer tasks
- `hook-quality-gate.ts` - Lint/format/typecheck on file changes

---

## Integration: Bottom-Up Meets Top-Down

The two approaches converge at the artifact registry:

```
                    TOP-DOWN
                       │
          ┌────────────▼────────────┐
          │   Pipeline Manifest     │
          │  (phases, transitions)  │
          └────────────┬────────────┘
                       │ sequences
          ┌────────────▼────────────┐
          │       Commands          │
          │  (artifact transforms)  │
          └────────────┬────────────┘
                       │ orchestrate
┌──────────────────────▼──────────────────────┐
│              BLUEPRINTS                      │
│  (Artifact, Author, Reviewer, Policies)    │
│                                              │
│  The integration point. Each blueprint      │
│  defines an artifact type with its          │
│  author/reviewer pair and policies.        │
└──────────────────────┬──────────────────────┘
                       │
                   BOTTOM-UP
```

**Key Insight:** Start bottom-up by defining one blueprint with `/wft:design-blueprint`. Or start top-down with a pipeline manifest and stub out the blueprints.

---

## Next Steps

### Phase 1: Define Primitives

- [x] Minimal policy structure → Follow §META
- [x] Minimal subagent structure → Frontmatter + Required Policies + Workflow + Output Format
- [x] Minimal command structure → Frontmatter + numbered steps + Important Rules
- [x] Artifact type declaration format → Explicit in `.claude/artifacts.yaml`

### Phase 2: Create Templates

- [ ] Bottom-up starter kit (3 files)
- [ ] Top-down starter kit (manifest + stubs)
- [ ] Template library by role/pattern

### Phase 3: Build Tooling

- [ ] Workflow validator (check manifest references)
- [ ] Workflow visualizer (manifest → diagram)
- [ ] Scaffold generator (template → files)

### Phase 4: Documentation

- [ ] Tutorial: Build your first workflow (bottom-up)
- [ ] Tutorial: Design a workflow (top-down)
- [ ] Reference: All template options
- [ ] Patterns: Common workflow patterns

---

## Design Decisions

### Resolved

| Decision | Resolution | Rationale |
|----------|------------|-----------|
| Organizing principle | Blueprint-centric | Everything flows from blueprints (artifact type + author + reviewer + policies) |
| Subagent pattern | Author/reviewer pairs | Universal pattern; every artifact type has exactly one author + one reviewer |
| Author naming | Semantic (architect, engineer, editor) | Meaningful names: architect=specs, engineer=code, editor=docs |
| Reviewer naming | `[artifact-type]-reviewer` | Consistent pattern tied to artifact type |
| Support subagents | Separate category | Explorers/investigators gather context, don't produce artifacts |
| Policy structure | Follow §META | Consistency with §META standards |
| Policy completeness | Internally complete, self-contained | §PY works alone; no structural dependencies |
| Policy injection | policy-fetch hook (required) | Makes `## Required Policies` functional |
| Artifact typing | Single type per artifact | A plan with code snippets is still governed by §PLAN |
| Blueprint extensibility | Design-time, not ad-hoc | New blueprints require: policy + file pattern + author + reviewer + command |
| Command purpose | Artifact transformation | Commands transform input artifacts to output artifacts |
| Command orchestration | `Task(@agent-name, "prompt")` | Single message with multiple Tasks for parallel execution |
| Output schema | Markdown templates | Human readable; convention-based structure |
| Manifest format | Markdown | Readable, allows prose, consistent with other artifacts |
| Manifest execution | Descriptive, not executable | Human-in-the-loop by design |

### Open

| Decision | Options | Considerations |
|----------|---------|----------------|
| Artifact registry format | Markdown table vs YAML file | Table is readable; YAML is machine-parseable |
| Validation timing | On-save vs. on-run | On-save catches errors early; on-run is simpler |

---

## References

- WFT bundled policies (`policies/`) - §META, §WORKFLOW, §AGENT standards
- WFT agents (`agents/`) - Subagent implementation examples
- WFT commands (`commands/`) - Command implementation examples
