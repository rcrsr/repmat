# Repmat

> **Experimental** — APIs and conventions may change. Requires Claude Code 1.0.33+.

A construction kit for building policy-governed agent workflows for Claude Code. Define artifact types, author/reviewer pairs, and policies—Repmat provides the primitives to assemble them.

## Quick Start

```bash
# Install the plugin
claude plugin install ~/projects/wft --scope user

# Design a new blueprint (greenfield)
/repmat:design-blueprint backend-code

# Or discover from existing code
/repmat:discover-blueprint backend-code --preset service

# Implement the blueprint (creates agents + registry entry)
/repmat:implement-blueprint docs/specs/backend-code-spec.md

# Generate policies from codebase patterns
/repmat:create-policies BACKEND src/api/
```

## Installation

```bash
# Local testing
claude --plugin-dir ~/projects/wft

# Project scope (commits to .claude/settings.json)
claude plugin install ~/projects/wft --scope project

# User scope (available in all projects)
claude plugin install ~/projects/wft --scope user
```

## Blueprints

Think about how teams work: an engineer writes code, a reviewer checks it, and both follow the team's style guide. Repmat captures this pattern as a **blueprint**.

A blueprint binds four things together:
- **Artifact type** — what gets created (code files, specs, docs)
- **Author agent** — who builds it
- **Reviewer agent** — who validates it
- **Policies** — what rules both follow

**Example**: You want coding agents for your Python backend. Define the blueprint in `registry.yaml`:

```yaml
artifacts:
  backend-code:
    patterns: ["src/api/**/*.py", "src/services/**/*.py"]
    author: backend-engineer
    reviewer: backend-code-reviewer
    policies: [python-style.md, api-patterns.md]
    auto_review: true
```

This single definition gives you:
- A `backend-engineer` agent that writes code following your policies
- A `backend-code-reviewer` agent that validates against the same policies
- Automatic review after creation (via `auto_review: true`)
- Pattern matching to identify which files belong to this artifact type

## Commands

### Blueprint Commands

| Command | Scenario | Description |
|---------|----------|-------------|
| `/repmat:design-blueprint <name>` | Greenfield | Design blueprint from scratch |
| `/repmat:discover-blueprint <name>` | Green/Brown | Infer blueprint from existing files |
| `/repmat:modify-blueprint <type>` | Brownfield | Modify existing blueprint |
| `/repmat:implement-blueprint <path>` | All | Implement blueprint (creates agents + registry) |

### Policy Commands

| Command | Description |
|---------|-------------|
| `/repmat:create-policies <prefix> [path]` | Create policy from codebase patterns |
| `/repmat:review-policies` | Review all policies against §META |

## Presets

Presets auto-detect patterns and trigger online exploration for current best practices.

| Category | Presets | Purpose |
|----------|---------|---------|
| Code | `code`, `service`, `frontend`, `mobile`, `desktop`, `cli`, `library` | Application code and packages |
| Data | `jupyter`, `schema` | Notebooks and database schemas |
| Infrastructure | `infrastructure` | IaC (CDK, Terraform, Kubernetes) |
| Documents | `document`, `spec`, `plan`, `requirements`, `runbook`, `api-docs` | Document types |

Usage: `/repmat:discover-blueprint backend-code --preset service`

## How It Works

### Architectural Layers

```
┌─────────────────────────────────────────────────────────────┐
│  Layer 5: Pipelines                                         │
│  Orchestrate multiple commands across phases                │
├─────────────────────────────────────────────────────────────┤
│  Layer 4: Commands                                          │
│  User-invocable workflows that transform artifacts          │
├─────────────────────────────────────────────────────────────┤
│  Layer 3: Subagents                                         │
│  Atomic units of work with policy awareness                 │
├─────────────────────────────────────────────────────────────┤
│  Layer 2: Policies                                          │
│  Codified knowledge governing artifacts and behavior        │
├─────────────────────────────────────────────────────────────┤
│  Layer 1: Artifacts                                         │
│  Typed work products—the foundation everything operates on  │
└─────────────────────────────────────────────────────────────┘
```

> **Note:** Layer 5 (Pipelines) is specified in `docs/workflow-toolkit.md` but not yet implemented. Pipelines will orchestrate multi-phase workflows with human-in-the-loop command sequencing.

> **Note:** The `auto_review` flag in registry entries is declarative only. Commands currently call reviewers explicitly in their steps. Automatic reviewer invocation after author completion is planned.

### Author/Reviewer Pairs

| Role | Naming | Purpose |
|------|--------|---------|
| architect | `[domain]-architect` | Creates specs/designs |
| engineer | `[domain]-engineer` | Creates code |
| editor | `[artifact]-editor` | Creates documents |
| reviewer | `[artifact]-reviewer` | Validates against policies |

Support agents (explorers, investigators) gather context but don't produce artifacts.

### Policy Fetching

The plugin includes a PreToolUse hook that injects policies before agent invocations:

1. **Plugin policies** (`${CLAUDE_PLUGIN_ROOT}/policies/`) - Base policies bundled with the plugin
2. **Project policies** (`$CLAUDE_PROJECT_DIR/policies/`) - Project-specific policies

This requires `@rcrsr/mcp-policy-server` to be available via npx.

### Bundled Policies

| Policy | File | Description |
|--------|------|-------------|
| §META | `policies-meta.md` | Policy document structure standards |
| §WFT | `policies-wft.md` | Workflow standards |
| §AGENT | `policies-agents.md` | Subagent configuration standards |

See `docs/repmat-framework.md` for the complete framework specification.

## Structure

```
wft/
├── .claude-plugin/
│   └── plugin.json              # Plugin metadata
├── registry.yaml                # Artifact registry
├── commands/                    # User-invocable slash commands
├── agents/                      # Subagent definitions
├── hooks/
│   └── hooks.json               # PreToolUse policy fetch
├── policies/                    # §META, §WFT, §AGENT
├── presets/                     # Dynamic guidance for artifact design
└── docs/
    └── repmat-framework.md      # Framework specification
```
