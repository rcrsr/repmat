# CLI

Preset for command-line interface tools. Auto-detects language and framework.

## Detection Signals

Identify CLI framework from:
- Python: `click`, `typer`, `argparse`, `fire` in dependencies
- Node/TS: `commander`, `yargs`, `oclif`, `cac` in dependencies
- Go: `cobra`, `urfave/cli`, `kong` imports
- Rust: `clap`, `structopt` in Cargo.toml

Identify patterns from:
- Subcommand structure
- Configuration file handling
- Plugin architecture

## Search Keywords

Based on detected framework:
- "[framework] CLI best practices"
- "[framework] subcommand patterns"
- "[framework] argument parsing"

Based on language:
- "[language] CLI design patterns"
- "[language] terminal UI libraries"

General CLI:
- "CLI design best practices"
- "command-line interface UX"
- "POSIX CLI conventions"
- "12 factor CLI apps"

## Investigation Focus

When analyzing local CLI code, examine:
- **Command structure** — How are commands/subcommands organized?
- **Argument parsing** — Flags, options, positional args, validation
- **Configuration** — Config files, environment variables, precedence
- **Output formatting** — Human vs machine output, colors, progress
- **Error handling** — Exit codes, error messages, help text
- **Interactive mode** — Prompts, confirmations, wizards
- **Shell integration** — Completions, aliases, shell scripts
- **Plugin architecture** — Extension points, plugin loading, sandboxing
- **Man pages** — Documentation generation, help formatting
- **Telemetry** — Usage analytics, opt-in/opt-out patterns

## Policy Considerations

Common sections for CLI policies:
- Command naming conventions
- Argument and flag patterns
- Configuration handling
- Output format (human/machine)
- Error handling and exit codes
- Help text standards
- Shell completion support
- Testing approach

## CI/CD Detection

Look for pipeline configurations:
- GitHub Actions: `.github/workflows/*.yml`
- Release automation: `goreleaser.yml`, `.releaserc`
- Package publishing: npm publish, PyPI upload, Homebrew formula

## Security Investigation

Examine security-related patterns:
- **Credential handling** — Keychain integration, secure storage
- **Input validation** — Path traversal prevention, injection protection
- **Privilege management** — Sudo usage, capability requirements
- **Update mechanism** — Secure update channels, signature verification

## Defaults

- **meta_type**: stateless
- **author_role**: engineer
- **typical_patterns**: `**/cli/**/*`, `**/commands/**/*`, `**/cmd/**/*`, `bin/**/*`
