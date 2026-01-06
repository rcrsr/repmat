# Library

Preset for reusable packages and libraries. Auto-detects package manager and distribution target.

## Detection Signals

Identify as library from:
- Package manifests with library indicators (not binaries/apps)
- Export patterns: `index.ts`, `lib/`, `src/index.js`
- No entry point for execution (no `main`, `bin` fields pointing to CLI)

Identify package ecosystem from:
- npm/pnpm/yarn: `package.json` with no `bin`, exports/main pointing to lib
- PyPI: `pyproject.toml` or `setup.py` with package metadata
- Cargo: `Cargo.toml` with `[lib]` section
- Go: `go.mod` with no `main` package in root
- Maven/Gradle: `pom.xml` or `build.gradle` with library packaging
- NuGet: `.csproj` with library output type
- RubyGems: `.gemspec` file

Identify distribution patterns from:
- Bundling: Rollup, esbuild, webpack configs for library output
- Type definitions: `.d.ts` generation, TypeScript declarations
- Documentation: API docs generation (TypeDoc, Sphinx, rustdoc)

## Search Keywords

Based on detected ecosystem:
- "[ecosystem] library best practices [year]"
- "[ecosystem] package publishing"
- "[ecosystem] semantic versioning"
- "[ecosystem] API design patterns"

General library patterns:
- "library API design best practices"
- "semantic versioning"
- "backwards compatibility patterns"
- "library documentation best practices"

## Investigation Focus

When analyzing local library code, examine:
- **API surface** — What is exported? Public vs internal APIs?
- **Versioning** — How are versions managed? Changelog maintained?
- **Bundling** — How is the library packaged? ESM/CJS/UMD?
- **Type definitions** — Are types exported? Generated or hand-written?
- **Documentation** — API docs, README, examples
- **Testing** — Unit tests, integration tests, API contract tests
- **Backwards compatibility** — Breaking change detection, deprecation patterns
- **Dependencies** — Peer vs direct dependencies, version ranges
- **Tree-shaking** — Are exports optimized for dead code elimination?

## Policy Considerations

Common sections for library policies:
- API design conventions
- Versioning strategy (SemVer)
- Breaking change process
- Deprecation policy
- Export structure
- Type definition requirements
- Documentation requirements
- Testing requirements
- Release process

## CI/CD Detection

Look for library pipeline configurations:
- GitHub Actions: `.github/workflows/*.yml` with publish steps
- npm: `.npmrc`, publish scripts in package.json
- Semantic Release: `.releaserc`, `release.config.js`
- Changesets: `.changeset/` directory
- PyPI: `twine` usage, `pyproject.toml` publish config

## Security Investigation

Examine security-related patterns:
- **Dependency audit** — npm audit, pip-audit, cargo-audit configs
- **Supply chain** — Package provenance, signing
- **Vulnerability disclosure** — SECURITY.md, security policy
- **License compliance** — License file, SPDX identifiers

## Defaults

- **meta_type**: stateless
- **author_role**: engineer
- **typical_patterns** (by ecosystem):
  - npm: `src/**/*.ts`, `lib/**/*.js`, `index.ts`
  - PyPI: `src/**/*.py`, `**/__init__.py`
  - Cargo: `src/**/*.rs`, `src/lib.rs`
  - Go: `**/*.go` (excluding `cmd/`)
  - Maven: `src/main/java/**/*.java`
