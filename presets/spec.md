# Spec

Preset for specification and design documents.

## Detection Signals

Identify as spec from:
- Filename patterns: `*-spec.md`, `*-design.md`, `*-rfc.md`, `*-adr.md`
- Location: `docs/specs/`, `docs/design/`, `docs/rfcs/`, `docs/adr/`
- Sections: "Overview", "Requirements", "Design", "Architecture"
- Status field with design states: Draft, In Review, Approved, Rejected

Identify machine-readable specs:
- OpenAPI: `openapi.yaml`, `swagger.json`
- AsyncAPI: `asyncapi.yaml`
- Protocol Buffers: `*.proto`
- JSON Schema: `*.schema.json`
- GraphQL: `schema.graphql`

## Search Keywords

- "software specification document format"
- "technical design document template"
- "RFC document format"
- "architecture decision record format"
- "design document best practices"

## Investigation Focus

When analyzing local specs, examine:
- **Document structure** — What sections are standard?
- **Requirements format** — How are requirements stated? Numbered? User stories?
- **Design rationale** — How are decisions documented?
- **Diagrams** — ASCII art, Mermaid, external images?
- **Status lifecycle** — Draft → Review → Approved → Deprecated flow
- **Acceptance criteria** — How is "done" defined?
- **Dependencies** — How are dependencies on other specs noted?
- **Code generation** — Is code generated from specs? What tools?
- **Validation** — Are specs validated (linting, schema checks)?

## Policy Considerations

Common sections for spec policies:
- Required sections (Overview, Requirements, Design)
- Requirements format
- Acceptance criteria format
- Design rationale documentation
- Diagram conventions
- Status lifecycle states
- Review process
- Versioning approach

## Defaults

- **meta_type**: status
- **states**: [Draft, In Review, Approved, Rejected, Superseded, Deprecated]
- **author_role**: architect
- **typical_patterns**: `docs/specs/*.md`, `docs/specifications/*.md`, `docs/rfcs/*.md`, `docs/adr/*.md`
