# Repmat

> **Experimental** — APIs and conventions may change. Requires Claude Code 1.0.33+.

Turn your software development tasks into reliable AI agents.

## What Is This?

The more you delegate to Claude Code, the harder consistency gets. Results drift between sessions. Styles clash. Rules you thought were clear get interpreted differently each time.

You can paste your standards into every prompt, but they drift out of sync. You can build custom subagents and commands, but they're either too generic or too specific and hard to share.

Repmat takes a different approach. Define your standards once as **blueprints**—templates that specify what gets built, who builds it, who reviews it, and what rules apply. From there, Repmat generates agents and commands that follow those blueprints consistently, and includes tools to design blueprints from scratch or discover them from existing codebases.

The pattern comes from how regular teams work: developers write code, reviewers check it, everyone follows the same style guide. A blueprint captures that dynamic for Claude Code.

A blueprint binds four things together:
- **Artifact type** — what gets created (code files, specs, docs)
- **Author agent** — who builds it
- **Reviewer agent** — who validates it
- **Policies** — what rules both follow

Say you want consistent API code for your Python backend. The *artifact type* is your API code—handlers, services, data access layers. The *author* is an engineer agent that writes new endpoints. The *reviewer* validates against your standards. And the *policies* spell out those standards: how to handle errors, where logging goes, what the service layer can import. Define this once, and Repmat generates and maintains agents and supporting commands and policies that follow it every time.

## How It Works

Everything starts with a blueprint—the four things: artifact type, author, reviewer, policies.

You can design a blueprint from scratch, or discover one from existing code. Repmat comes with presets for common use cases—backend services, frontend components, infrastructure, documentation—so you're not starting from zero. When discovering from existing code, Repmat analyzes your codebase, identifies patterns, and proposes a blueprint that fits what's already there.

Once you have a blueprint, Repmat implements it. It generates the author and reviewer agents, wires up the policies, and creates commands to invoke them. You don't write boilerplate—Repmat scaffolds the pieces and keeps them consistent with your blueprint.

Now you can use these new subagents and commands to maintain your artifacts—writing Python API endpoints, generating infrastructure configs, updating documentation, or whatever else you designed the blueprint for.

For the full architecture, see [the framework specification](docs/repmat-framework.md).

## Quick Start

### Install

```bash
/plugin marketplace add rcrsr/claude-plugins
/plugin install repmat@rcrsr
```

### Use

```bash
# Design a new blueprint (greenfield)
/repmat:design-blueprint backend-code

# Or discover from existing code
/repmat:discover-blueprint backend-code --preset service

# Implement the blueprint (creates agents + registry entry)
/repmat:implement-blueprint docs/specs/backend-code-spec.md

# Generate policies from codebase patterns
/repmat:create-policies BACKEND src/api/
```

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
| `/repmat:review-policies` | Validate policy documents against formatting standards |

## Presets

Presets provide detection signals and web search queries to find current best practices for common artifact types.

| Category | Presets | Purpose |
|----------|---------|---------|
| Code | `code`, `service`, `frontend`, `mobile`, `desktop`, `cli`, `library` | Application code and packages |
| Data | `jupyter`, `schema` | Notebooks and database schemas |
| Infrastructure | `infrastructure` | IaC (CDK, Terraform, Kubernetes) |
| Documents | `document`, `spec`, `plan`, `requirements`, `runbook`, `api-docs` | Document types |

Usage: `/repmat:discover-blueprint backend-code --preset service`
