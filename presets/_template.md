# Preset Template

Copy this template to create a new preset. Remove sections that don't apply to your preset type.

---

# [Preset Name]

[One sentence describing when to use this preset]

## Detection Signals

How to identify when this preset applies:
- [File patterns, extensions, markers]
- [Package files, dependencies]
- [Framework/tool indicators]

Identify variants from:
- [Variant A]: [detection patterns]
- [Variant B]: [detection patterns]

## Search Keywords

Topics to explore online for current best practices:
- "[topic] best practices [year]"
- "[topic] conventions patterns"
- "[topic] project structure"

Based on detected variant:
- "[variant A] patterns best practices"
- "[variant B] conventions"

## Investigation Focus

When analyzing local artifacts, examine:
- **[Aspect 1]** — What to look for, why it matters
- **[Aspect 2]** — What to look for, why it matters
- **[Aspect 3]** — What to look for, why it matters

## Policy Considerations

Sections commonly needed in policies for this type:
- [Section topic 1]
- [Section topic 2]
- [Section topic 3]

## CI/CD Detection

(Include for code presets)

Look for pipeline configurations:
- GitHub Actions: `.github/workflows/*.yml`
- [Tool-specific CI]: [config file patterns]

## Security Investigation

(Include for code presets)

Examine security-related patterns:
- **[Security aspect 1]** — What to look for
- **[Security aspect 2]** — What to look for

## Defaults

- **meta_type**: [stateless | status | tasklist]
- **author_role**: [architect | engineer | editor]
- **typical_patterns** (by variant):
  - [Variant A]: `[glob pattern]`, `[glob pattern]`
  - [Variant B]: `[glob pattern]`

---

## Template Usage Notes

### Required Sections (all presets)
- Detection Signals
- Search Keywords
- Investigation Focus
- Policy Considerations
- Defaults

### Code Preset Sections (add for code artifacts)
- CI/CD Detection
- Security Investigation

### Document Preset Sections (add for document artifacts)
- States list in Defaults (for status-tracked documents)

### Specificity Guidelines

1. **Detection Signals**: List concrete file patterns, config files, dependency markers. Avoid vague descriptions.

2. **typical_patterns**: Always enumerate specific glob patterns per variant. Never use "Language-dependent" or similar placeholders.

3. **Investigation Focus**: Use bold aspect names with em-dash descriptions. Include 8-12 aspects for thorough coverage.

4. **Search Keywords**: Include `[year]` placeholder to encourage searching for current practices.

### Preset Categories

| Category | Example Presets | meta_type | author_role |
|----------|-----------------|-----------|--------------|
| Code | code, service, frontend, mobile, desktop, cli, library | stateless | engineer |
| Data | jupyter, schema | stateless | engineer |
| Infrastructure | infrastructure | stateless | engineer |
| Documents | document, spec, plan, requirements, runbook, api-docs | status/tasklist | editor/architect |

### Combining Presets

Presets can apply together. When analyzing a codebase:
- Full-stack app: `frontend` + `service` + `schema`
- Mobile backend: `mobile` + `service`
- Published package: `library` + `code`

Document this in the preset if relevant combinations exist.
