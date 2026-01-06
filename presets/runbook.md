# Runbook

Preset for operational documentation, incident response, and standard operating procedures.

## Detection Signals

Identify as runbook from:
- Filename patterns: `*-runbook.md`, `*-playbook.md`, `*-sop.md`
- Location: `docs/runbooks/`, `docs/operations/`, `docs/playbooks/`
- Sections: "Prerequisites", "Steps", "Rollback", "Escalation"
- Procedural structure: Numbered steps, checklists, decision trees

Identify runbook type from:
- Incident response: "Incident", "Alert", "Severity", "On-call"
- Deployment: "Deploy", "Release", "Rollback"
- Maintenance: "Maintenance", "Upgrade", "Migration"
- Troubleshooting: "Debug", "Diagnose", "Symptoms"

## Search Keywords

- "runbook best practices"
- "incident response documentation"
- "SRE runbook template"
- "operational playbook format"
- "standard operating procedure format"
- "troubleshooting guide template"

## Investigation Focus

When analyzing local runbooks, examine:
- **Structure consistency** — Do all runbooks follow the same format?
- **Prerequisites** — Are requirements clearly stated?
- **Step format** — Numbered steps? Commands included? Expected outputs?
- **Decision points** — How are conditional paths documented?
- **Rollback procedures** — Are rollback steps included for each action?
- **Escalation paths** — Who to contact? When to escalate?
- **Automation links** — References to scripts, dashboards, alerts
- **Last review date** — How current is the documentation?
- **Testing** — Are runbooks tested? Drills documented?

## Policy Considerations

Common sections for runbook policies:
- Required sections (Overview, Prerequisites, Steps, Rollback)
- Step format requirements
- Command/output documentation
- Escalation path requirements
- Review frequency
- Testing/drill requirements
- Link to monitoring/alerting
- Change management for runbook updates

## Defaults

- **meta_type**: status
- **states**: [Draft, Active, Under Review, Deprecated]
- **author_role**: editor
- **typical_patterns**: `docs/runbooks/*.md`, `docs/operations/*.md`, `docs/playbooks/*.md`
