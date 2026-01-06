# CLAUDE.md

## Project Overview

Repmat is a construction kit for building policy-governed agent workflows. Define artifact types, create coding standards (policies), and assemble specialized AI agents for different code domains.

## Architectural Layers

```
Layer 5: Pipelines       → Orchestrate multiple commands across phases
Layer 4: Commands        → User-invocable workflows that transform artifacts
Layer 3: Subagents       → Atomic tasks with policy awareness
Layer 2: Policies        → Codified knowledge governing artifacts and behavior
Layer 1: Artifacts       → Typed work products—the foundation everything operates on
```

**Key principle**: Artifacts are foundational. Policies govern them, subagents transform them, commands orchestrate transformations, pipelines sequence commands.

## Artifact-Centric Model

Each artifact type is defined by exactly one blueprint:

```
(Artifact Type, Author, Reviewer, Policies)
```

**Author/Reviewer pairs** — every artifact type has exactly one author and one reviewer:

| Role | Naming | Creates |
|------|--------|---------|
| architect | `[domain]-architect` | specs/designs |
| engineer | `[domain]-engineer` | code |
| editor | `[artifact]-editor` | documents |
| reviewer | `[artifact]-reviewer` | validates artifacts |

**Support agents** (explorers, investigators) gather context but don't produce persistent artifacts.

## Plugin Structure

```
wft/
├── .claude-plugin/plugin.json  # Plugin metadata (name, version)
├── registry.yaml               # Artifact registry (types, authors, reviewers, policies)
├── commands/                   # User-invocable slash commands
├── agents/                     # Subagent definitions (author/reviewer pairs)
├── hooks/hooks.json            # PreToolUse policy fetch hook
├── policies/                   # Bundled policies (§META, §WFT, §AGENT)
├── presets/                    # Blueprint presets (detection signals, defaults, guidance)
└── docs/repmat-framework.md    # Framework specification
```

## Testing the Plugin

```bash
# Local testing (loads plugin for current session)
claude --plugin-dir ~/projects/wft

# Install to project scope (commits to .claude/settings.json)
claude plugin install ~/projects/wft --scope project

# Install to user scope (available in all projects)
claude plugin install ~/projects/wft --scope user
```

## Commands

### Blueprint Commands

| Command | Scenario | Purpose |
|---------|----------|---------|
| `/repmat:design-blueprint <name> [--preset X]` | Greenfield | Design blueprint from scratch |
| `/repmat:discover-blueprint <name> [--preset X]` | Green/Brown | Infer blueprint from existing files |
| `/repmat:modify-blueprint <type>` | Brownfield | Modify existing blueprint |
| `/repmat:implement-blueprint <path>` | All | Implement blueprint (creates agents + registry) |

Use `--preset` to load detection signals, defaults, and guidance for common artifact types.

### Policy Commands

| Command | Purpose |
|---------|---------|
| `/repmat:create-policies <prefix>` | Create policy from codebase patterns |
| `/repmat:review-policies` | Review all policies against §META |

## Agents

| Artifact | Author | Reviewer |
|----------|---------|----------|
| repmat-spec | `repmat-architect` | `repmat-spec-reviewer` |
| command | `repmat-command-engineer` | `repmat-command-reviewer` |
| agent | `repmat-subagent-engineer` | `repmat-subagent-reviewer` |
| policy | `repmat-policy-engineer` | `repmat-policy-reviewer` |

Support agent: `repmat-domain-analyzer` (analyzes codebases, no reviewer)

## Artifact Registry

Artifacts are defined in `registry.yaml` with the blueprint: `(Artifact Type, Author, Reviewer, Policies)`

```yaml
artifacts:
  <artifact-type>:
    patterns: [<glob>, ...]      # File patterns
    author: <subagent-name>     # Author subagent
    reviewer: <subagent-name>    # Reviewer subagent
    policies: [<file>, ...]      # Governing policies
    auto_review: <boolean>       # Trigger reviewer after author
```

**Artifact flow**: `[Input] → Command → Author → [Output] → Reviewer → Verdict`

## Policy System

Policies use hierarchical `§PREFIX.N` notation. Bundled policies:
- `§META` (`policies-meta.md`) - Policy document structure standards
- `§WFT` (`policies-wft.md`) - Workflow standards (artifacts, subagents, commands, pipelines)
- `§AGENT` (`policies-agents.md`) - Subagent design and configuration standards

The PreToolUse hook in `hooks/hooks.json` auto-fetches policies before Task invocations. The hook reads all policy files from `$CLAUDE_PROJECT_DIR/policies/*.md` and `${CLAUDE_PLUGIN_ROOT}/policies/*.md`, but only injects policies explicitly referenced in the subagent's `## Required Policies` section.

## Presets

Presets provide detection signals, defaults, and investigation guidance for common artifact types. Used with `--preset` in blueprint commands.

| Category | Presets |
|----------|---------|
| Code | `service`, `frontend`, `mobile`, `desktop`, `cli`, `library`, `code` |
| Data | `schema`, `jupyter` |
| Infrastructure | `infrastructure` |
| Documents | `document`, `spec`, `plan`, `requirements`, `runbook`, `api-docs` |

Each preset contains:
- **Detection Signals** — file patterns and framework markers
- **Search Keywords** — web search queries for best practices
- **Investigation Focus** — what to analyze in existing files
- **Defaults** — `meta_type`, `author_role`, `typical_patterns`

Example: `/repmat:discover-blueprint backend-api --preset service`

## Key Conventions

**Command files** (`commands/*.md`):
- YAML frontmatter with `description` and optional `argument-hint`
- Numbered steps: `## Step N: Action Verb`
- Use `Task(@agent-name, "prompt")` syntax for delegation
- End with next-command recommendation

**Agent files** (`agents/*.md`):
- YAML frontmatter: `name`, `description` (include "PROACTIVELY" for auto-invoke), `tools`
- `## Required Policies` section with JSON array of § references
- 100-200 lines target; over 250 indicates over-specification
- Never specify `model:` in frontmatter (caller selects)

**Policy files** (`policies/*.md`):
- Required: Title, introduction, TOC, sections with `{§PREFIX.X}` headers, `{§END}` marker
- Patterns over implementations (examples under 15 lines)
- Self-contained; reference other policies with § notation instead of duplicating

## Workflow Execution Order

1. `/repmat:analyze-code-domains` - Discover domains in target codebase
2. `/repmat:create-domain-policies <domain>` - Create/identify policies per domain
3. `/repmat:create-domain-coding-subagents <domain>` - Generate architect/engineer/reviewer triplet

## Required Infrastructure

The **policy-fetch hook** is required infrastructure—it makes `## Required Policies` sections functional by injecting policy content at subagent invocation time.

```
Command → Task(@agent) → policy-fetch hook → inject policies → execute
```

## Dependencies

- Claude Code 1.0.33+
- `@rcrsr/mcp-policy-server` (for policy fetching hook)

## Reference

See `docs/repmat-framework.md` for the complete framework specification including:
- Layer architecture details
- Author/reviewer pair patterns
- Artifact registry and flow patterns
- Minimum viable workflow examples
