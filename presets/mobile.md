# Mobile

Preset for mobile application code. Auto-detects platform and framework.

## Detection Signals

Identify platform/framework from:
- iOS Native: `.swift` files, `Package.swift`, Xcode project files
- Android Native: `.kt`/`.java` files, `build.gradle`, Android manifest
- React Native: `react-native` in dependencies, Metro config
- Flutter: `pubspec.yaml`, `.dart` files, Flutter project structure
- Expo: `expo` in dependencies, `app.json` with expo config
- Capacitor/Ionic: `capacitor.config.ts`, `@capacitor/*` dependencies

## Search Keywords

Based on detected platform:
- "[platform] best practices [year]"
- "[platform] architecture patterns"
- "[platform] performance optimization"

Platform-specific:
- iOS: "SwiftUI patterns", "UIKit best practices", "iOS app architecture"
- Android: "Jetpack Compose patterns", "Android architecture components"
- React Native: "React Native performance", "native modules patterns"
- Flutter: "Flutter state management", "Flutter architecture patterns"

General mobile:
- "mobile app architecture patterns"
- "offline-first patterns"
- "mobile security best practices"

## Investigation Focus

When analyzing local mobile code, examine:
- **App architecture** — MVC, MVVM, Clean Architecture, Redux pattern
- **Navigation** — Navigation patterns, deep linking, tab/stack navigation
- **State management** — How is app state managed? Persistence?
- **Native integration** — Platform APIs, native modules, permissions
- **Offline support** — Local storage, sync patterns, conflict resolution
- **Performance** — List rendering, image loading, memory management
- **Platform conventions** — Following platform HIG/Material Design

## Policy Considerations

Common sections for mobile policies:
- Architecture pattern (MVVM, Clean, etc.)
- Navigation patterns
- State management approach
- Native API usage
- Offline/sync patterns
- Performance requirements
- Platform-specific conventions
- Testing (unit, widget/component, e2e)

## CI/CD Detection

Look for mobile CI configurations:
- Fastlane: `fastlane/`, `Fastfile`, `Appfile`
- GitHub Actions: `.github/workflows/*.yml`
- Bitrise: `bitrise.yml`
- App Center: `appcenter-post-clone.sh`

## Security Investigation

Examine security-related patterns:
- **Secrets handling** — API keys in code, keychain usage, secure storage
- **Certificate management** — Signing configs, provisioning profiles
- **Network security** — Certificate pinning, TLS configuration
- **Data protection** — Encryption at rest, secure enclave usage

## Additional Investigation Focus

- **Push notifications** — FCM/APNs integration, notification handling
- **Analytics/crash reporting** — Firebase, Sentry, Crashlytics integration
- **Deep linking** — URL schemes, universal links, app links
- **App store metadata** — Screenshots, descriptions, versioning strategy

## Defaults

- **meta_type**: stateless
- **author_role**: engineer
- **typical_patterns** (by platform):
  - iOS Native: `**/*.swift`, `**/*.m`, `**/*.h`, `*.xcodeproj/**/*`
  - Android Native: `**/*.kt`, `**/*.java`, `app/src/**/*`
  - React Native: `**/*.tsx`, `**/*.ts`, `src/**/*`, `android/**/*`, `ios/**/*`
  - Flutter: `**/*.dart`, `lib/**/*`, `android/**/*`, `ios/**/*`
  - Expo: `**/*.tsx`, `**/*.ts`, `app/**/*`, `src/**/*`
