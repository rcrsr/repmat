# Agent Design and Development Policies

Project-agnostic principles for designing, developing, and deploying AI agents and subagents.

## Table of Contents

- [Overview](#agent1-overview)
- [Architecture Patterns](#agent2-architecture-patterns)
  - [Workflows vs Agents](#agent21-workflows-vs-agents)
  - [Pipeline Pattern](#agent22-pipeline-pattern)
  - [Orchestrator-Worker Pattern](#agent23-orchestrator-worker-pattern)
  - [Parallel Processing Pattern](#agent24-parallel-processing-pattern)
- [Design Principles](#agent3-design-principles)
  - [Single Responsibility](#agent31-single-responsibility)
  - [Specialization Criteria](#agent32-specialization-criteria)
- [Configuration](#agent4-configuration)
  - [File Organization](#agent41-file-organization)
  - [Required Fields](#agent42-required-fields)
  - [Optional Fields](#agent43-optional-fields)
  - [System Prompt Design](#agent44-system-prompt-design)
- [Model Selection](#agent5-model-selection)
- [Tool Management](#agent6-tool-management)
  - [Permission Matrix](#agent61-permission-matrix)
  - [Tool Design](#agent62-tool-design)
- [Policy Integration](#agent7-policy-integration)
  - [Required Policies Section](#agent71-required-policies-section)
  - [Section Reference Notation](#agent72-section-reference-notation)
- [Prompt Engineering](#agent8-prompt-engineering)
  - [Clarity and Directness](#agent81-clarity-and-directness)
  - [Example-Driven Documentation](#agent82-example-driven-documentation)
  - [Context Over Rules](#agent83-context-over-rules)
- [Context Management](#agent9-context-management)
  - [Quality Over Quantity](#agent91-quality-over-quantity)
  - [Context Clearing](#agent92-context-clearing)
  - [CLAUDE.md Files](#agent93-claudemd-files)
- [Anti-Patterns](#agent10-anti-patterns)
  - [Design Anti-Patterns](#agent101-design-anti-patterns)
  - [Runtime Anti-Patterns](#agent102-runtime-anti-patterns)
- [Evaluation](#agent11-evaluation)
  - [Success Criteria](#agent111-success-criteria)
  - [Metrics](#agent112-metrics)
- [Advanced Techniques](#agent12-advanced-techniques)
  - [Headless Automation](#agent121-headless-automation)
  - [Thinking Modes](#agent122-thinking-modes)
  - [Planning vs Execution](#agent123-planning-vs-execution)

## {§AGENT.1} Overview

Agent design balances simplicity with specialization for optimal performance and maintainability.

**Core Philosophy:**

- Build the right system for needs, not the most sophisticated
- Context quality matters more than context quantity
- Measure before adding complexity
- Explicit delegation over implicit coordination

**Key Principles:**

| Principle          | Section  | Summary                                     |
| ------------------ | -------- | ------------------------------------------- |
| Specialization     | §AGENT.3 | Single-purpose agents with clear boundaries |
| Configuration      | §AGENT.4 | Structured setup with YAML frontmatter      |
| Model Selection    | §AGENT.5 | Match model to task complexity              |
| Tool Management    | §AGENT.6 | Minimal permissions for security            |
| Policy Guidance    | §AGENT.7 | Explicit policy fetching for standards      |
| Context Management | §AGENT.9 | Maintain high signal-to-noise ratio         |

## {§AGENT.2} Architecture Patterns

Common patterns for structuring multi-agent systems.

### {§AGENT.2.1} Workflows vs Agents

| Aspect  | Workflows                 | Agents                |
| ------- | ------------------------- | --------------------- |
| Control | Predefined code paths     | Dynamic LLM decisions |
| Tasks   | Well-defined, predictable | Open-ended, adaptive  |
| Cost    | Lower latency and cost    | Higher flexibility    |
| Debug   | Easier to maintain        | Complex coordination  |

**Selection:** Use workflows for predictable tasks; agents for open-ended problems requiring reasoning.

### {§AGENT.2.2} Pipeline Pattern

Sequential processing with handoff points between specialized stages.

```
Stage 1: Specification → Stage 2: Architecture → Stage 3: Implementation
```

**Stage agents:** Each stage has a dedicated agent with specific tools and outputs a flag (`READY_FOR_ARCH`, `READY_FOR_IMPL`, `DONE`) when complete.

**Use cases:** Feature development requiring design review, migrations with architectural impact.

### {§AGENT.2.3} Orchestrator-Worker Pattern

Central LLM analyzes tasks and routes to specialized subagents.

```
Orchestrator (Sonnet)
    ├─> Worker 1 (Haiku)
    ├─> Worker 2 (Haiku)
    └─> Worker N (Haiku)
```

**Routing logic:**

| Route By   | Haiku            | Sonnet                 |
| ---------- | ---------------- | ---------------------- |
| Complexity | Straightforward  | Complex reasoning      |
| Domain     | Implementation   | Architecture, security |
| Operation  | Write operations | Design decisions       |

**Benefits:** 90% of Sonnet performance at 67% cost reduction.

### {§AGENT.2.4} Parallel Processing Pattern

Multiple subagents work on independent tasks simultaneously.

**Requirements:**

- Explicit delegation with clear task boundaries
- Non-overlapping file modifications
- Independent work units

```python
# Good: Explicit boundaries
["Create component in src/UserProfile.tsx",
 "Add styles in src/UserProfile.css",
 "Write tests in tests/UserProfile.test.tsx"]

# Bad: Vague (causes serial execution)
"Create user profile with components, styles, and tests"
```

## {§AGENT.3} Design Principles

### {§AGENT.3.1} Single Responsibility

Each agent excels at one task type.

| Scope      | Example                                 | Assessment      |
| ---------- | --------------------------------------- | --------------- |
| Good       | `security-auditor`: OWASP Top 10 review | Complete domain |
| Too narrow | `xss-checker`: Only XSS                 | Fragment        |
| Too broad  | `code-reviewer`: All quality aspects    | Unfocused       |

**Balance criteria:** Agent handles complete workflow for its domain; task complexity justifies dedicated agent; clear boundaries with other agents.

### {§AGENT.3.2} Specialization Criteria

| Create Specialized Agent When    | Use General-Purpose When        |
| -------------------------------- | ------------------------------- |
| Unique system prompt needed      | One-off task                    |
| Tool permissions differ          | No special restrictions         |
| Domain expertise improves output | Generic instructions sufficient |
| Repeated invocation              | Standard model appropriate      |

## {§AGENT.4} Configuration

### {§AGENT.4.1} File Organization

Store agent configurations in Markdown files with YAML frontmatter.

```
project/.claude/agents/security-auditor.md
~/.claude/agents/debugger.md  # User-level default
```

**Priority:** Project-level agents override user-level when names conflict.

**Naming:** Lowercase with hyphens, 2-3 words: `security-auditor.md`

### {§AGENT.4.2} Required Fields

```yaml
---
name: security-auditor # Lowercase, hyphens
description: Reviews code for security vulnerabilities using OWASP Top 10
---
```

**Description rules:**

- Natural language purpose statement
- Include "use PROACTIVELY" for automatic task detection
- Clear scope boundaries

### {§AGENT.4.3} Optional Fields

```yaml
tools: Read, Grep, Bash # Comma-separated; omit for all tools
```

**Model field:** Do NOT specify `model:` in frontmatter. Agents inherit from delegating agent (§AGENT.5).

### {§AGENT.4.4} System Prompt Design

**Principles:**

- Avoid repeating inherited context
- Include examples for complex behaviors
- Target 100-200 lines; >250 lines indicates over-specification (§AGENT.10.1)

**Include:** Required Policies section, workflow (3-5 steps), 1 output example, success criteria.

**Exclude:** Detailed code examples, checklists, decision tables (policies provide these).

## {§AGENT.5} Model Selection

Agents inherit model from delegating agent. NEVER specify `model:` in frontmatter.

**Decision Matrix:**

| Task Type         | Model  | Justification                         |
| ----------------- | ------ | ------------------------------------- |
| Simple CRUD       | Haiku  | Straightforward, high frequency       |
| Test generation   | Haiku  | Pattern-based, clear requirements     |
| Refactoring       | Haiku  | Mechanical changes, clear scope       |
| Bug investigation | Sonnet | Complex reasoning required            |
| Architecture      | Sonnet | Strategic decisions, tradeoffs        |
| Security audit    | Sonnet | Subtle vulnerabilities, deep analysis |

**Cost comparison:**

| Model  | Input       | Output       | Speed       |
| ------ | ----------- | ------------ | ----------- |
| Haiku  | $1/M tokens | $5/M tokens  | 4-5x faster |
| Sonnet | $3/M tokens | $15/M tokens | Baseline    |

**Hybrid pattern:** Sonnet orchestrator routes to Haiku workers. Achieves 90% performance at 67% cost reduction.

## {§AGENT.6} Tool Management

### {§AGENT.6.1} Permission Matrix

Grant minimum permissions for agent role.

| Role Category      | Role                 | Tools                   |
| ------------------ | -------------------- | ----------------------- |
| **Analysis**       | Security auditor     | Read, Grep              |
|                    | Code reviewer        | Read, Grep, Bash        |
|                    | Performance profiler | Read, Bash              |
| **Implementation** | Feature developer    | Read, Write, Edit, Bash |
|                    | Bug fixer            | Read, Edit, Bash        |
|                    | Migration agent      | Read, Write, Edit, Bash |
| **Documentation**  | Technical writer     | Read, Write             |
|                    | Comment generator    | Read, Edit              |
| **Testing**        | Test generator       | Read, Write, Bash       |
|                    | Test runner          | Read, Bash              |

### {§AGENT.6.2} Tool Design

**Principles:**

| Principle             | Implementation                              |
| --------------------- | ------------------------------------------- |
| Familiar formats      | JSON for data, Markdown for docs            |
| Reduce cognitive load | Direct answers, not raw data                |
| Prevent mistakes      | Absolute paths, enums over strings          |
| Single purpose        | One well-designed tool > multiple redundant |

## {§AGENT.7} Policy Integration

### {§AGENT.7.1} Required Policies Section

Agents declare required policies in a dedicated section; the system auto-fetches them.

```yaml
---
name: code-reviewer
description: Reviews code for standards compliance
tools: Read, Grep
---
# Code Reviewer

## Required Policies

["§BASIC.2", "§BASIC.3", "§TS.1-4"]
## Workflow
...
```

**Rules:**

- Header: Always `## Required Policies`
- Content: JSON array of § section references
- Use range notation: `§BASIC.1-8`
- Place immediately after agent title, before workflow

**Prohibition:** NEVER copy policy content into agent prompts. Reference § sections only.

### {§AGENT.7.2} Section Reference Notation

```
§PREFIX.NUMBER[.SUBSECTION]

§BASIC.2      - Top-level section
§BASIC.2.1    - Subsection
§TS.3.1       - TypeScript patterns
```

**Available prefixes:** §BASIC, §TS, §PY, §MONO, §AGENT, §BE, §FE, §CDK

**Range notation:** `§BASIC.2-5` expands to §BASIC.2, §BASIC.3, §BASIC.4, §BASIC.5

**Fencing:** Placeholder references in output templates MUST be in code blocks to prevent fetch attempts.

## {§AGENT.8} Prompt Engineering

### {§AGENT.8.1} Clarity and Directness

| Bad                  | Good                                                                            |
| -------------------- | ------------------------------------------------------------------------------- |
| "Create dashboard"   | "Create analytics dashboard with user metrics, date range selector, CSV export" |
| "Fix the bug"        | "Fix auth bug where users stay logged in after session expiration"              |
| "NEVER use ellipses" | "Response read by TTS; ellipses won't pronounce. Use complete sentences."       |

### {§AGENT.8.2} Example-Driven Documentation

Provide 1-2 examples showing desired behavior. Include edge cases.

```yaml
# Generate TypeScript interfaces from JSON schemas
# Input: { "name": "string", "age": "number" }
# Output: interface User { name: string; age: number; }
```

### {§AGENT.8.3} Context Over Rules

Explain why rules exist, not just what they are.

| Bad                             | Good                                                                      |
| ------------------------------- | ------------------------------------------------------------------------- |
| "Never use var"                 | "Use const/let: var has function scope causing leaks and hoisting issues" |
| "Always include error handling" | "Handle errors to prevent data loss and enable debugging"                 |

## {§AGENT.9} Context Management

### {§AGENT.9.1} Quality Over Quantity

Context with 10% irrelevant content performs worse than 50% context with all relevant content.

| High Quality        | Low Quality           |
| ------------------- | --------------------- |
| Relevant code files | Stale command outputs |
| Recent conversation | Abandoned experiments |
| Clear requirements  | Old resolved errors   |

**Subagent isolation:** Delegate exploration to subagents returning condensed summaries (1,000-2,000 tokens). Main context stays clean.

### {§AGENT.9.2} Context Clearing

Use `/clear` frequently to prevent noise accumulation.

| When to Clear                         |
| ------------------------------------- |
| After completing discrete feature     |
| When switching codebase areas         |
| Before starting unrelated task        |
| When noticing performance degradation |

**Preservation:** Copy important decisions to CLAUDE.md before clearing.

### {§AGENT.9.3} CLAUDE.md Files

Store persistent context that survives `/clear`.

```
/project/CLAUDE.md           # Project-specific
/monorepo/CLAUDE.md          # Shared across services
~/.claude/CLAUDE.md          # Global patterns
```

**Content guidelines:** Under 100 lines; patterns not details; update as architecture evolves.

## {§AGENT.10} Anti-Patterns

### {§AGENT.10.1} Design Anti-Patterns

| Anti-Pattern        | Problem                                | Solution                                 |
| ------------------- | -------------------------------------- | ---------------------------------------- |
| Over-fragmentation  | Micro-agents for trivial tasks         | Combine tasks <3 ops into main agent     |
| Under-specification | Missing success criteria, paths        | Define boundaries, criteria, constraints |
| Over-specification  | Prompt >250 lines, embedded checklists | Reference policies, 1 example max        |
| Policy duplication  | Examples/checklists in prompts         | Use `## Required Policies` section       |

### {§AGENT.10.2} Runtime Anti-Patterns

| Anti-Pattern             | Symptom                             | Prevention                       |
| ------------------------ | ----------------------------------- | -------------------------------- |
| Resource conflicts       | Parallel agents edit same file      | Assign non-overlapping files     |
| Context pollution        | Stale references, drift             | `/clear` when switching focus    |
| Thinking loops           | Repeated reasoning without progress | Clear goal, success criteria     |
| Scope creep              | Implementing unrequested features   | Explicit task boundaries         |
| Unguided trial and error | Random attempts                     | Hypothesis-test-conclude pattern |

## {§AGENT.11} Evaluation

### {§AGENT.11.1} Success Criteria

Define before building agent. Keep brief: 3-5 bullet points.

| Category     | Example Criteria                                |
| ------------ | ----------------------------------------------- |
| Correctness  | Output matches requirements, edge cases handled |
| Completeness | All requirements addressed, error paths covered |
| Efficiency   | <500 tokens per task, <10s response time        |

```
Agent succeeds when:
- All files follow §TS.4 import organization
- No policy violations remain uncited
- Output uses structured finding format
```

### {§AGENT.11.2} Metrics

| Metric           | Formula                        |
| ---------------- | ------------------------------ |
| Token efficiency | total_tokens / task_count      |
| Success rate     | successful_tasks / total_tasks |
| Cost per task    | total_cost / task_count        |

**Iteration:** Define criteria → implement → measure → identify failures → improve → re-measure.

## {§AGENT.12} Advanced Techniques

### {§AGENT.12.1} Headless Automation

```bash
# Basic execution
claude -p "Run test suite and report failures"

# Structured output
claude -p "Analyze coverage" --output-format stream-json

# Fan out for migrations
for file in src/**/*.py; do
  claude -p "Migrate $file to new API" &
done
wait
```

**YOLO mode:** `--dangerously-skip-permissions` for containerized, no-internet, low-risk operations only.

### {§AGENT.12.2} Thinking Modes

Scale reasoning depth for problem complexity.

| Mode         | Use Case                                |
| ------------ | --------------------------------------- |
| Standard     | CRUD, simple refactoring                |
| think        | Bug investigation, basic architecture   |
| think hard   | Complex refactoring, optimization       |
| think harder | Security analysis, system design        |
| ultrathink   | Novel algorithms, critical architecture |

### {§AGENT.12.3} Planning vs Execution

Separate exploration from implementation.

**Phase 1 (Haiku):** Explore codebase, identify patterns, propose approach. Output: condensed plan (1,000-2,000 tokens).

**Phase 2 (Sonnet):** Implement following plan with focused context.

**Benefits:** Planning noise isolated; clean execution context; easier plan review; failed plans don't pollute.

{§END}
