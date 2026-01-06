# Requirements

Preset for requirements and feature request documents.

## Detection Signals

Identify as requirements from:
- Filename patterns: `*-requirements.md`, `*-feature.md`, `*-prd.md`
- Location: `docs/requirements/`, `docs/features/`
- Sections: "User Stories", "Acceptance Criteria", "Use Cases"
- Format markers: "As a [user], I want [goal]" patterns

## Search Keywords

- "software requirements document format"
- "product requirements document template"
- "user story format best practices"
- "acceptance criteria format"
- "functional requirements documentation"
- "BDD given-when-then format"

## Investigation Focus

When analyzing local requirements docs, examine:
- **Requirements format** — User stories? Use cases? Numbered requirements?
- **Acceptance criteria** — Given-when-then? Checklist? Prose?
- **Priority notation** — How are requirements prioritized? MoSCoW? P0-P3?
- **Traceability** — IDs? Links to specs/implementation?
- **Stakeholder context** — Who are the personas/actors?
- **Scope boundaries** — In-scope vs out-of-scope notation
- **Success metrics** — How is success measured?
- **Non-functional requirements** — Performance, security, scalability criteria
- **Change tracking** — How are requirement changes versioned?
- **Issue integration** — Links to JIRA, Linear, GitHub Issues?

## Policy Considerations

Common sections for requirements policies:
- Document structure (Overview, Stories, Criteria)
- User story format
- Acceptance criteria format
- Priority classification
- Traceability requirements
- Scope documentation
- Success metrics

## Defaults

- **meta_type**: status
- **states**: [Draft, Ready, In Progress, Implemented, Validated]
- **author_role**: editor
- **typical_patterns**: `docs/requirements/*.md`
