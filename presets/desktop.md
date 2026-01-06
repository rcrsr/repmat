# Desktop

Preset for desktop application code. Auto-detects framework and platform.

## Detection Signals

Identify framework from:
- Electron: `electron` in dependencies, main/renderer process files
- Tauri: `tauri.conf.json`, `src-tauri/` directory, Rust backend
- Qt: `.pro` files, Qt includes, QML files
- .NET/WPF: `.csproj` with WPF references, XAML files
- GTK: `gtk` dependencies, Glade files
- SwiftUI (macOS): `.swift` files with SwiftUI imports, macOS target

Identify patterns from:
- IPC mechanism: Electron IPC, Tauri commands, native bindings
- State management: In main process vs renderer
- Auto-updates: Electron-updater, Tauri updater, Sparkle

## Search Keywords

Based on detected framework:
- "[framework] best practices [year]"
- "[framework] architecture patterns"
- "[framework] security best practices"
- "[framework] performance optimization"

General desktop:
- "desktop application architecture"
- "native integration patterns"
- "desktop app security best practices"
- "cross-platform desktop patterns"

## Investigation Focus

When analyzing local desktop code, examine:
- **Process architecture** — Main/renderer split, IPC patterns, sandboxing
- **Native integration** — System APIs, file system, notifications, tray
- **Window management** — Multi-window patterns, state persistence
- **Auto-updates** — Update mechanism, delta updates, rollback
- **Security** — Content security policy, context isolation, permissions
- **Performance** — Startup time, memory usage, native bindings
- **Packaging** — Build process, installers, code signing

## Policy Considerations

Common sections for desktop policies:
- Process architecture (main/renderer, IPC)
- Native API usage patterns
- Security requirements (CSP, isolation)
- Window and state management
- Auto-update patterns
- Packaging and distribution
- Testing approach

## CI/CD Detection

Look for desktop CI configurations:
- GitHub Actions: `.github/workflows/*.yml`
- Electron Builder: `electron-builder.yml`
- Tauri: `.github/workflows/*tauri*.yml`

## Security Investigation

Examine security-related patterns:
- **Code signing** — Certificate configs, notarization scripts
- **Update security** — Signature verification, secure update channels
- **Sandbox configuration** — Entitlements, capabilities, permissions
- **Local storage security** — Encrypted storage, credential management

## Additional Investigation Focus

- **Crash reporting** — Sentry, Crashlytics, Electron crashReporter
- **License/activation** — License key validation, trial management
- **Auto-launch** — Startup registration, login items
- **System integration** — File associations, protocol handlers, context menus

## Defaults

- **meta_type**: stateless
- **author_role**: engineer
- **typical_patterns** (by framework):
  - Electron: `src/main/**/*`, `src/renderer/**/*`, `**/*.ts`, `**/*.tsx`
  - Tauri: `src/**/*`, `src-tauri/**/*.rs`, `**/*.ts`, `**/*.tsx`
  - Qt: `**/*.cpp`, `**/*.h`, `**/*.qml`, `**/*.pro`
  - .NET/WPF: `**/*.cs`, `**/*.xaml`
  - GTK: `**/*.c`, `**/*.vala`, `**/*.ui`
  - SwiftUI (macOS): `**/*.swift`
