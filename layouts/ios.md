<!-- build layout catalogue — profile ios (RULES §15). First edition. Copy blocks verbatim; replace <App> (UpperCamel app name) and <Feat> (UpperCamel feature name). -->

# Layout profile `ios` — iOS app

**Status:** first edition · **Sources:** Apple's Swift Package Manager and Xcode documentation (local packages, test targets), Swift Testing / XCTest.

## 1. Skeleton

```
<code_root>/
  AGENTS.md  CLAUDE.md  README.md
  <App>.xcodeproj/          thin app project: app target, assets, entitlements, Info.plist
  <App>/                    app target sources: <App>App.swift — composition root only
  Packages/
    Core/                   Package.swift, Sources/Core/, Tests/CoreTests/
    <Feat>/                 one local Swift package per build feature
      Package.swift
      Sources/<Feat>/       views, view models, services of the feature
      Tests/<Feat>Tests/
  planning/
  config/keys.toml          settings (layouts/_tools.md)
  tools/                    dev.py, layout_check.py, secret_check.py
  scripts/
```

The app project stays thin: almost all code lives in packages, which keeps the `.xcodeproj` small (fewer merge conflicts, fewer files agents must edit through Xcode) and makes each feature testable on its own with `swift test` where it has no UIKit / SwiftUI dependency.

`Packages/<Feat>/Package.swift`:

```swift
// swift-tools-version: 5.10
import PackageDescription

let package = Package(
    name: "<Feat>",
    platforms: [.iOS(.v17)],
    products: [.library(name: "<Feat>", targets: ["<Feat>"])],
    dependencies: [.package(path: "../Core")],          // + the features it may use (concept §11)
    targets: [
        .target(name: "<Feat>", dependencies: ["Core"]),
        .testTarget(name: "<Feat>Tests", dependencies: ["<Feat>"]),
    ]
)
```

## 2. Mapping

| build term | In this profile |
| --- | --- |
| feature | local Swift package `Packages/<Feat>/` |
| core | package `Packages/Core/` — depends on no feature package |
| entry point / composition root | the app target (`<App>App.swift`): builds the dependency graph, shows the first screen |
| public interface of a feature | `public` declarations of its library product |
| module (concept §11) | one Swift file with one responsibility |

## 3. Dependency rule and its check

Every allowed dependency is one entry in a package's `dependencies`; SwiftPM rejects imports of undeclared packages and cycles. The lists equal concept §11 — reviewed at Phase 6. `tools/layout_check.py`: `ALLOWED_TOP` with `Packages`, `<App>`, `config` (the `.xcodeproj` folder starts with the app name — add it too); `SOURCE_DIRS = ["Packages", "<App>"]`.

## 4. Tests

| Kind | Location | Runner | One test |
| --- | --- | --- | --- |
| unit | `Packages/<Feat>/Tests/<Feat>Tests/` | Swift Testing or XCTest | `python tools/dev.py test -only-testing:<Feat>Tests/<Suite>/<test>` |
| UI | `<App>UITests/` target | XCUITest | `python tools/dev.py test -only-testing:<App>UITests` |
| on device | `planning/build/manual-tests.md` | people | MT rows with device model and iOS version in the execution log |

## 5. Execution surface

`tools/dev.py` PROFILE block:

```python
PKG = ""
TEST = ["xcodebuild", "test", "-scheme", "<App>", "-destination", "platform=iOS Simulator,name=iPhone 16"]
LAYOUT = [sys.executable, "tools/layout_check.py"]
LINT = [["swiftlint", "--strict"], ["swift-format", "lint", "--recursive", "Packages"]]
TOOLS = ["xcodebuild", "swift", "swiftlint", "swift-format", "gitleaks", "pre-commit", "git"]
REQUIRED = ["git", "xcodebuild"]
```

## 6. Hygiene additions

```gitignore
# --- layout ios ---
/build/
DerivedData/
xcuserdata/
*.xcuserstate
.swiftpm/
/Packages/*/.build/
*.ipa
*.dSYM.zip
*.mobileprovision
*.p12
```

Secrets: never in `Info.plist`, an `.xcconfig` that is committed, or the binary — anything in the app bundle can be extracted. Server-side secrets stay on the server; user tokens go into the Keychain. Signing certificates and provisioning profiles stay out of the repository.

## 7. Agent limits

**macOS only.** `xcodebuild`, the simulator and code signing do not exist in a Linux sandbox. There, synthesis writes package code and tests but records every slice as "not verified here — run `python tools/dev.py test` on a Mac"; the slice is green only after the user reports the result. Pure-logic packages without UIKit / SwiftUI may run with `swift test` on Linux where a Swift toolchain is installed. App Store submission and signing are always the user's.

## 8. Error convention

How failures are signalled, propagated and caught (RULES §16). Every handling site carries out an E row of concept §11.

- **`throws` with typed errors.** One error enum per package conforming to `Error` (typed throws where the Swift version allows); `Result` only for completion handlers. An optional return means "not found", never "failed".
- **No `try!` and no force unwrap** outside tests; `try?` only where the row's action is "skip" and the skip is logged.
- **Context travels.** Associated values carry what the detector knows (item, step, E-ID); the handler logs them.
- **Caught where the action is.** `do`/`catch` takes the row's action where it can be taken; the UI layer maps errors to user messages. The catch site names the row: `// E-<FEAT>-3`.
- **Check:** SwiftLint `force_try` and `force_unwrapping` (opt-in rule) enabled, run by `LINT`.
- **Invariant checks before every side effect** throw the product's error — never `assert()`, which optimised builds strip; an invariant check is an ordinary detector with its own matrix row. Each side-effecting call goes through one action-boundary helper that maps an unexpected error to the action's standing row (concept §11 reaction matrix); besides it, the only catch-all is at an entry point.
