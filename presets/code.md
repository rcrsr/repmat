# Code

General preset for any code artifact. Auto-detects language and framework.

## Detection Signals

Identify language from:
- File extensions: `.py`, `.ts`, `.js`, `.go`, `.rs`, `.java`, `.cs`, `.cpp`, `.rb`, `.c`, `.swift`
- Package files: `pyproject.toml`, `package.json`, `go.mod`, `Cargo.toml`, `pom.xml`
- Lock files: `uv.lock`, `pnpm-lock.yaml`, `Cargo.lock`

Identify framework from:
- Import statements and dependencies
- Configuration files
- Directory structure patterns

## Search Keywords

Based on detected language, search:
- "[language] best practices [year]"
- "[language] code style conventions"
- "[language] project structure"
- "[language] type system patterns" (if applicable)

If framework detected:
- "[framework] patterns best practices"
- "[framework] project structure"

## Investigation Focus

When analyzing local code, examine:
- **Naming conventions** — Files, functions, classes, variables. Consistent style?
- **Import organization** — How are imports grouped and ordered?
- **Type usage** — Are types/annotations used? How consistently?
- **Error handling** — Patterns for exceptions, error returns, Result types
- **Testing patterns** — Test organization, fixtures, mocking approach
- **Documentation** — Docstrings, comments, inline documentation style

## Policy Considerations

Common sections for code policies:
- Naming conventions
- File organization
- Import ordering
- Type annotation requirements
- Error handling patterns
- Testing requirements
- Documentation standards

## CI/CD Detection

Look for pipeline configurations:
- GitHub Actions: `.github/workflows/*.yml`
- GitLab CI: `.gitlab-ci.yml`
- CircleCI: `.circleci/config.yml`
- Jenkins: `Jenkinsfile`

## Security Investigation

Examine security-related patterns:
- **Secrets handling** — Environment variables, vault integration, .env files
- **Dependency scanning** — Dependabot, Snyk, audit configs
- **Linting/formatting** — ESLint, Prettier, Ruff, gofmt, rustfmt configs
- **Code generation markers** — `// Code generated`, `@generated`, `DO NOT EDIT`

## Defaults

- **meta_type**: stateless
- **author_role**: engineer
- **typical_patterns** (by language):
  - Python: `**/*.py`, `src/**/*.py`, `lib/**/*.py`
  - TypeScript/JavaScript: `**/*.ts`, `**/*.tsx`, `**/*.js`, `src/**/*`
  - Go: `**/*.go`, `cmd/**/*.go`, `pkg/**/*.go`, `internal/**/*.go`
  - Rust: `**/*.rs`, `src/**/*.rs`
  - Java: `**/*.java`, `src/main/**/*.java`
  - C#: `**/*.cs`, `src/**/*.cs`
  - C/C++: `**/*.c`, `**/*.cpp`, `**/*.h`, `**/*.hpp`
  - Ruby: `**/*.rb`, `lib/**/*.rb`
  - Swift: `**/*.swift`, `Sources/**/*.swift`
