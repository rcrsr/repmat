# Document

General preset for document artifacts. Auto-detects document type and structure.

## Detection Signals

Identify document type from:
- Status field: `**Status:**` indicates lifecycle-tracked document
- Task markers: `[ ]`/`[x]` indicates plan or checklist
- Section patterns: "Requirements", "Acceptance Criteria" → requirements doc
- Section patterns: "Architecture", "Design Decisions" → spec/design doc
- Section patterns: "Phase", "Tasks", "Timeline" → plan doc

Identify structure from:
- Heading hierarchy
- Numbered vs bulleted sections
- Tables vs prose
- Code blocks and examples

Identify documentation tooling from:
- MkDocs: `mkdocs.yml`
- Docusaurus: `docusaurus.config.js`
- Sphinx: `conf.py`, `index.rst`
- VitePress: `.vitepress/config.ts`
- GitBook: `SUMMARY.md`, `.gitbook.yaml`

## Search Keywords

Based on detected type:
- "[doc-type] document template"
- "[doc-type] best practices"
- "technical [doc-type] format"

General documentation:
- "technical writing best practices"
- "documentation structure patterns"
- "markdown documentation conventions"

## Investigation Focus

When analyzing local documents, examine:
- **Structure consistency** — Do similar docs follow same structure?
- **Status tracking** — How is document lifecycle tracked?
- **Cross-references** — How do docs reference each other?
- **Metadata** — Title, status, author, date patterns
- **Section patterns** — Common sections across docs
- **Formatting** — Tables, code blocks, callouts
- **Versioning** — Document version history, changelog
- **Auto-generation** — API refs, TOC, generated sections
- **Publication** — How are docs published? Static site? PDF?

## Policy Considerations

Common sections for document policies:
- Required metadata (title, status, date)
- Section structure
- Formatting conventions
- Cross-reference patterns
- Status lifecycle
- Review requirements

## Defaults

- **meta_type**: status (most documents track lifecycle)
- **author_role**: editor
- **typical_patterns**: `docs/**/*.md`
