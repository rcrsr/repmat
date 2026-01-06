# Jupyter

Preset for Jupyter notebooks and computational documents.

## Detection Signals

Identify as Jupyter from:
- File extensions: `.ipynb`
- Notebook metadata: kernel specification, language info
- JupyterLab/JupyterHub config files

Identify notebook type from:
- Data science: pandas, numpy, matplotlib imports
- Machine learning: scikit-learn, tensorflow, pytorch imports
- Analysis: statistical libraries, visualization heavy
- Documentation: markdown-heavy, tutorial style
- Research: academic citations, LaTeX equations

Identify workflow patterns from:
- Papermill: `parameters` cell tag, parameterized execution
- Quarto: `.qmd` files, YAML frontmatter in notebooks
- nbconvert: Export configurations, custom templates
- Jupyter Book: `_toc.yml`, `_config.yml`

## Search Keywords

Based on detected type:
- "Jupyter notebook best practices [year]"
- "data science notebook organization"
- "[ML framework] notebook patterns"

General Jupyter:
- "Jupyter notebook structure best practices"
- "reproducible notebooks"
- "notebook version control"
- "Jupyter cell organization"

## Investigation Focus

When analyzing local notebooks, examine:
- **Cell organization** — Logical flow? Markdown headers? Cell length?
- **Import patterns** — All imports at top? Scattered throughout?
- **Output handling** — Are outputs cleared? Large outputs stored?
- **Magic commands** — Which magics are used? Consistent patterns?
- **Documentation** — Markdown cells explaining code? Inline comments?
- **Reproducibility** — Seeds set? Dependencies documented? Clear execution order?
- **Visualization** — Plot styling consistent? Figures labeled?
- **Data handling** — Where is data loaded from? Paths hardcoded?
- **Parameterization** — Papermill tags, injectable parameters
- **Testing** — nbval, pytest-notebook usage
- **Export workflows** — nbconvert configs, CI export steps

## Policy Considerations

Common sections for Jupyter policies:
- Notebook structure (imports, setup, analysis, conclusions)
- Cell organization (max lines per cell, markdown requirements)
- Output management (clear outputs, size limits)
- Reproducibility requirements (seeds, environment, execution order)
- Documentation standards (markdown cells, docstrings)
- Visualization conventions
- Data path handling
- Version control practices (.ipynb vs .py export)

## CI/CD Detection

Look for notebook pipeline configurations:
- GitHub Actions: `.github/workflows/*.yml` with notebook execution
- Papermill: Parameterized execution in CI
- nbconvert: Export steps in pipelines
- Pre-commit: `nbstripout`, notebook linting hooks

## Defaults

- **meta_type**: stateless
- **author_role**: engineer
- **typical_patterns**: `**/*.ipynb`, `notebooks/**/*`, `*.qmd`
