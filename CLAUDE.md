# CLAUDE.md

Repmat: construction kit for policy-governed agent workflows. See @docs/repmat-framework.md for architecture.

## Testing

```bash
# Local testing
claude --plugin-dir ~/projects/wft

# Install to project/user scope
claude plugin install ~/projects/wft --scope project
claude plugin install ~/projects/wft --scope user
```

## Commands

| Command | Purpose |
|---------|---------|
| `/repmat:design-blueprint <name> [--preset X]` | Design blueprint from scratch |
| `/repmat:discover-blueprint <name> [--preset X]` | Infer blueprint from existing files |
| `/repmat:modify-blueprint <type>` | Modify existing blueprint |
| `/repmat:implement-blueprint <path>` | Implement blueprint (creates agents + registry) |
| `/repmat:create-policies <prefix>` | Create policy from codebase patterns |
| `/repmat:review-policies` | Review policies against §META |

## Agents

| Artifact | Author | Reviewer |
|----------|--------|----------|
| repmat-spec | `repmat-architect` | `repmat-spec-reviewer` |
| command | `repmat-command-engineer` | `repmat-command-reviewer` |
| agent | `repmat-subagent-engineer` | `repmat-subagent-reviewer` |
| policy | `repmat-policy-engineer` | `repmat-policy-reviewer` |

Support: `repmat-domain-analyzer` (analyzes codebases, no reviewer)

## Presets

Available: `service`, `web`, `mobile`, `desktop`, `cli`, `library`, `code`, `schema`, `jupyter`, `infrastructure`, `document`, `spec`, `plan`, `requirements`, `runbook`, `api-docs`

Usage: `--preset service` with blueprint commands

## Key Conventions

**Commands** (`commands/*.md`):
- YAML frontmatter: `description`, optional `argument-hint`
- Numbered steps: `## Step N: Action Verb`
- Delegation: `Task(@agent-name, "prompt")`
- End with next-command recommendation

**Agents** (`agents/*.md`):
- YAML frontmatter: `name`, `description`, `tools`
- Include "PROACTIVELY" in description for auto-invoke
- `## Required Policies` section with JSON array of § references
- Target 100-200 lines; over 250 indicates over-specification
- Never specify `model:` (caller selects)

**Policies** (`policies/*.md`):
- Required: Title, TOC, `{§PREFIX.X}` headers, `{§END}` marker
- Patterns over implementations (examples under 15 lines)
- Reference other policies with § notation; don't duplicate

## Plugin Structure

```
├── .claude-plugin/plugin.json  # Metadata
├── registry.yaml               # Artifact registry
├── commands/                   # Slash commands
├── agents/                     # Subagent definitions
├── hooks/hooks.json            # Policy fetch hook
├── policies/                   # §META, §WFT, §AGENT
├── presets/                    # Blueprint presets
└── docs/repmat-framework.md    # Full specification
```

## Dependencies

- `@rcrsr/mcp-policy-server`
