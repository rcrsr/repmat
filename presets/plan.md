# Plan

Preset for implementation and project plans.

## Detection Signals

Identify as plan from:
- Filename patterns: `*-plan.md`, `*-implementation.md`
- Location: `docs/plans/`
- Task markers: `[ ]` and `[x]` checkboxes
- Phase structure: "## Phase 1", "## Phase 2" headings
- Dependency references: blockers, prerequisites, sequencing

Identify structure type:
- Single-phase: Flat task list with checkboxes
- Multi-phase: Phase headings containing task lists

## Search Keywords

- "implementation plan format"
- "project plan document template"
- "technical task breakdown"
- "software development planning"
- "milestone planning best practices"

## Investigation Focus

When analyzing local plans, examine:
- **Task structure** — Flat list or phased? Hierarchical?
- **Task format** — Titles, descriptions, owners (no time estimates)
- **Progress tracking** — Checkboxes? Percentages? Status labels?
- **Dependencies** — How are task dependencies noted? Mermaid diagrams?
- **Phase boundaries** — What defines a phase? Exit criteria?
- **Risk tracking** — Are risks/blockers documented?
- **Completion criteria** — How is plan completion defined?

**Important**: Plans track tasks and dependencies, not timelines. Time estimates provide false precision with variable resources.

## Policy Considerations

Common sections for plan policies:
- Phase structure (if multi-phase)
- Task format requirements (no time estimates)
- Progress tracking method
- Dependency notation (text, Mermaid diagrams)
- Risk/blocker documentation
- Completion criteria
- Update frequency

## Defaults

- **meta_type**: tasklist
- **tasklist_type**: multiphase (or single for simple plans)
- **author_role**: editor
- **typical_patterns**: `docs/plans/*.md`
