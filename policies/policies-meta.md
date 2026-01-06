# Meta Policies

Standards for writing and maintaining policy documentation.

## Table of Contents

- [Overview](#meta1-overview)
  - [Policy Boundaries](#meta11-policy-boundaries)
- [Document Structure](#meta2-document-structure)
  - [Required Elements](#meta21-required-elements)
  - [Section Numbering](#meta22-section-numbering)
  - [Table of Contents](#meta23-table-of-contents)
- [Content Guidelines](#meta3-content-guidelines)
  - [Patterns Over Implementations](#meta31-patterns-over-implementations)
  - [Concise Examples](#meta32-concise-examples)
  - [Tables for Comparisons](#meta33-tables-for-comparisons)
  - [Cross-References](#meta34-cross-references)
- [Section Design](#meta4-section-design)
  - [Subsection Limits](#meta41-subsection-limits)
  - [Consolidation Triggers](#meta42-consolidation-triggers)
  - [Granularity Guidelines](#meta43-granularity-guidelines)
- [Maintenance](#meta5-maintenance)
  - [Review Triggers](#meta51-review-triggers)
  - [Streamlining Process](#meta52-streamlining-process)
  - [Deprecation](#meta53-deprecation)

## {§META.1} Overview

Policy documents guide code quality and consistency. Effective policies are concise, actionable, and maintainable.

**Core Philosophy:**

- Policies are reference guides, not tutorials
- State what to do, not how to learn
- Patterns over complete implementations
- Concise over comprehensive

**Policy Prefixes:**

| Prefix | Domain                 |
| ------ | ---------------------- |
| §BASIC | Core coding principles |
| §MONO  | Monorepo patterns      |
| §PY    | Python development     |
| §TS    | TypeScript development |
| §FE    | Frontend architecture  |
| §BE    | Backend architecture   |
| §CDK   | CDK infrastructure     |
| §AGENT | Agent design           |
| §SPEC  | Specifications         |
| §REQ   | Requirements           |
| §BRIEF | Engineering briefs     |
| §PLAN  | Implementation plans   |
| §WORKFLOW | Workflow artifacts  |
| §META  | Policy documentation   |

### {§META.1.1} Policy Boundaries

Each policy domain has a defined scope. Avoid overlap by adhering to boundaries.

**Language vs Architecture policies:**

| Aspect | Language Policy (§PY, §TS) | Architecture Policy (§BE, §FE) |
| ------ | -------------------------- | ------------------------------ |
| Focus | HOW to write code | WHERE to put code |
| Contains | Syntax, types, testing | Module structure, decomposition |
| Examples | Type hints, async patterns | Directory layout, service boundaries |
| Decisions | Code style, error handling | When to split, what to extract |

**Domain boundaries:**

| Domain | Scope | Contains | Does NOT Contain |
| ------ | ----- | -------- | ---------------- |
| §PY | Python code patterns | Syntax, testing, FastAPI/Pydantic usage | Module boundaries, deployment |
| §BE | Backend architecture | Service decomposition, auth architecture, deployment | Python syntax, test mechanics |
| §TS | TypeScript code patterns | Types, async patterns, language features | Component structure, state management |
| §FE | Frontend architecture | React patterns, styling, state | TypeScript syntax details |
| §CDK | CDK infrastructure | AWS resources, stacks, constructs | General TypeScript patterns |

**Boundary test questions:**

| Question | Domain |
| -------- | ------ |
| "How do I write a type hint?" | §PY |
| "Where does this service go?" | §BE |
| "When should I split a module?" | §BE |
| "How do I test this function?" | §PY |
| "What security measures are needed?" | §BE |
| "How do I implement auth in FastAPI?" | §PY |
| "What React patterns should I use?" | §FE |
| "How do I type a generic function?" | §TS |

**Cross-reference obligations:**

When content spans domains, reference rather than duplicate:

- §PY.7 (Code Organization) references §BE.2 (Module Organization)
- §BE.7 (Integration Patterns) contains adapter architecture
- §PY.9 (Auth Patterns) references §BE.8 (Security Patterns)

## {§META.2} Document Structure

### {§META.2.1} Required Elements

Every policy document must include:

1. **Title** - `# Domain Policies`
2. **Introduction** - One-sentence description
3. **Table of Contents** - Linked section hierarchy
4. **Sections** - Content with `{§PREFIX.X}` notation
5. **End marker** - `{§END}` at document end

**Template:**

```markdown
# Domain Policies

Brief description of policy domain.

## Table of Contents

- [Overview](#prefix1-overview)
- [Section Name](#prefix2-section-name)

## {§PREFIX.1} Overview

Content here.

{§END}
```

### {§META.2.2} Section Numbering

Use hierarchical notation with `§` prefix:

```
§PREFIX.X       - Major section
§PREFIX.X.Y     - Subsection
§PREFIX.X.Y.Z   - Sub-subsection (avoid if possible)
```

**Rules:**

- Prefix matches document domain (PY, TS, MONO, etc.)
- Numbers are sequential within level
- Wrap in `{§...}` in headers for policy server parsing
- Maximum 3 levels deep

**Examples:**

```markdown
## {§PY.6} Testing Standards # Major section

### {§PY.6.1} Test Organization # Subsection
```

### {§META.2.3} Table of Contents

Link all sections using markdown anchors:

```markdown
- [Testing Standards](#py6-testing-standards)
  - [Test Organization](#py61-test-organization)
  - [Pytest Configuration](#py62-pytest-configuration)
```

**Rules:**

- Mirror section hierarchy exactly
- Update TOC when sections change
- Anchor format: lowercase, numbers only, no dots

## {§META.3} Content Guidelines

### {§META.3.1} Patterns Over Implementations

State patterns, not complete implementations. Readers should understand **what** to do, not receive copy-paste code.

| Bad                            | Good                              |
| ------------------------------ | --------------------------------- |
| 100-line script implementation | 10-line pattern showing structure |
| Full GitHub Actions workflow   | Key configuration options         |
| Complete test file             | Test pattern with one example     |

**Example - Bad (implementation):**

```python
# 50 lines of sync script with error handling,
# logging, file iteration, regex patterns...
```

**Example - Good (pattern):**

```python
# scripts/sync-versions.ts syncs root version to subpackages
# Triggers: manual, pre-commit hook, CI/CD
```

### {§META.3.2} Concise Examples

Examples demonstrate patterns, not complete solutions.

**Rules:**

- Maximum 15 lines per code block
- Show one concept per example
- Omit obvious boilerplate
- Use comments for elided code: `# ... validation logic`

**Example:**

```python
# Good: Shows pattern, not full implementation
@pytest.fixture
def client(session: Session):
    app.dependency_overrides[get_db] = lambda: session
    with TestClient(app) as client:
        yield client
    app.dependency_overrides.clear()
```

### {§META.3.3} Tables for Comparisons

Use tables for good/bad comparisons, anti-patterns, and options.

**Format:**

```markdown
| Anti-Pattern              | Problem                    | Solution         |
| ------------------------- | -------------------------- | ---------------- |
| Caching without profiling | Complexity without benefit | Measure first    |
| HashMap for 10 items      | Overkill                   | Use simple array |
```

**When to use tables:**

- Good vs bad examples
- Problem → solution mappings
- Configuration options
- Decision criteria

### {§META.3.4} Cross-References

Reference other policies instead of duplicating content.

**Format:** Use `§PREFIX.X.Y` notation inline.

```markdown
Apply YAGNI principles (§BASIC.3) when designing agents.
```

**Rules:**

- Reference, don't duplicate
- Link to most specific applicable section
- Validate references exist before committing

## {§META.4} Section Design

### {§META.4.1} Subsection Limits

**Target:** 4-6 subsections per major section.

| Subsections | Action                       |
| ----------- | ---------------------------- |
| 1-3         | Consider merging into parent |
| 4-6         | Optimal range                |
| 7-9         | Consider consolidating       |
| 10+         | Must consolidate             |

### {§META.4.2} Consolidation Triggers

Consolidate when:

- Section exceeds 200 lines
- Subsection count exceeds 8
- Content duplicates other sections
- Examples dominate over principles
- Implementation details exceed patterns

**Consolidation techniques:**

1. Merge related subsections
2. Replace verbose examples with tables
3. Extract implementation to actual scripts
4. Reference existing policies instead of duplicating

### {§META.4.3} Granularity Guidelines

**Major sections (§PREFIX.X):**

- One coherent topic
- 50-150 lines typical
- 4-6 subsections typical

**Subsections (§PREFIX.X.Y):**

- One specific aspect
- 10-40 lines typical
- Self-contained guidance

**Avoid:**

- Subsections under 5 lines (merge up)
- Subsections over 100 lines (split or consolidate)
- More than 2 levels of nesting

## {§META.5} Maintenance

### {§META.5.1} Review Triggers

Review policies when:

- Total lines exceed 2,000 per document
- Adding new major section
- Patterns change significantly
- Users report confusion
- Quarterly maintenance cycle

### {§META.5.2} Streamlining Process

1. **Measure:** Count lines, sections, subsections
2. **Identify:** Find verbose examples, duplications, excessive subsections
3. **Consolidate:** Merge sections, use tables, trim examples
4. **Update:** Sync TOC with changes
5. **Validate:** Verify section references still work

**Targets:**

| Metric                  | Target   | Action if Exceeded          |
| ----------------------- | -------- | --------------------------- |
| Document lines          | < 2,000  | Streamline verbose sections |
| Subsections per section | 4-6      | Consolidate                 |
| Lines per example       | < 15     | Trim to pattern             |
| Total policy lines      | < 10,000 | Cross-document review       |

### {§META.5.3} Deprecation

When removing or renaming sections:

1. **Redirect:** Add note pointing to new location
2. **Grace period:** Keep redirect for one release cycle
3. **Remove:** Delete after grace period
4. **Update references:** Search codebase for old section numbers

**Deprecation notice format:**

```markdown
> **Deprecated:** This section moved to §NEW.X.Y. Remove reference by YYYY-MM-DD.
```

{§END}
