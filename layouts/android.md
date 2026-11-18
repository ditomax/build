<!-- build layout catalogue — profile android (RULES §15). First edition. Copy blocks verbatim; replace <pkg> (Kotlin package root, e.g. com.example.app) and <feat>. -->

# Layout profile `android` — Android app

**Status:** first edition · **Sources:** Android developer guide to app modularization, the "Now in Android" reference app (feature / core modules, version catalog, convention plugins), Gradle documentation.

## 1. Skeleton

```
<code_root>/
  AGENTS.md  CLAUDE.md  README.md
  settings.gradle.kts       includes every module
  build.gradle.kts          root: plugins only
  gradle/libs.versions.toml version catalog — the single place for dependency versions
  gradle/wrapper/           committed Gradle wrapper
  gradlew  gradlew.bat
  app/                      :app — Application, navigation, dependency injection: composition root only
  core/
    model/  data/  ui/      :core:<x> modules — shared kernel, depend on no feature
  feature/
    <feat>/                 :feature:<feat> — one module per build feature
      build.gradle.kts
      src/main/kotlin/<pkg path>/feature/<feat>/
      src/test/kotlin/…     JVM unit tests
      src/androidTest/kotlin/…   instrumented tests
  planning/
  config/keys.toml          settings (layouts/_tools.md)
  tools/                    dev.py, layout_check.py, secret_check.py
  scripts/
```

`feature/<feat>/build.gradle.kts` (minimal; a convention plugin may replace the repeated parts):

```kotlin
plugins {
    alias(libs.plugins.android.library)
    alias(libs.plugins.kotlin.android)
}

android {
    namespace = "<pkg>.feature.<feat>"
    compileSdk = 35
    defaultConfig { minSdk = 26 }
}

dependencies {
    implementation(project(":core:model"))     // + the core modules it uses
    implementation(project(":core:ui"))
    // never another :feature module — features talk through :app navigation or a :core contract
    testImplementation(libs.junit)
}
```

## 2. Mapping

| build term | In this profile |
| --- | --- |
| feature | Gradle module `:feature:<feat>` |
| core | `:core:<x>` modules — depend on no feature module |
| entry point / composition root | `:app` — the only module that depends on all features |
| public interface of a feature | its public Kotlin API and its navigation entry; everything else `internal` |
| module (concept §11) | one Kotlin file with one responsibility |

## 3. Dependency rule and its check

Every allowed dependency is one `project(…)` line; Gradle rejects cycles, and `internal` hides what is not the interface. Rule (stricter than L1, the Android convention): **a feature module never depends on another feature module**. `tools/layout_check.py`: `ALLOWED_TOP` with `app`, `core`, `feature`, `gradle`, `config`; `SOURCE_DIRS = ["app", "core", "feature"]`; `IGNORE` with `build`. Optional: Konsist or a dependency-analysis plugin as an architecture test (Y2).

## 4. Tests

| Kind | Location | Runner | One test |
| --- | --- | --- | --- |
| unit (JVM) | `feature/<feat>/src/test/` | JUnit via Gradle | `./gradlew :feature:<feat>:testDebugUnitTest --tests '*<Name>*'` (all: `python tools/dev.py test`) |
| instrumented | `feature/<feat>/src/androidTest/` | AndroidX Test on emulator / device | `./gradlew :feature:<feat>:connectedDebugAndroidTest` |
| on device | `planning/build/manual-tests.md` | people | MT rows with device model and Android version in the execution log |

## 5. Execution surface

`tools/dev.py` PROFILE block:

```python
PKG = ""
TEST = ["./gradlew", "testDebugUnitTest"]
LAYOUT = [sys.executable, "tools/layout_check.py"]
LINT = [["./gradlew", "lintDebug"], ["./gradlew", "ktlintCheck"]]
TOOLS = ["java", "adb", "sdkmanager", "gitleaks", "pre-commit", "git"]
REQUIRED = ["git", "java", "settings.gradle.kts"]
```

## 6. Hygiene additions

```gitignore
# --- layout android ---
.gradle/
# anchored where possible: a bare "build/" would also ignore planning/build/
/build/
/app/build/
/core/*/build/
/feature/*/build/
local.properties
*.apk
*.aab
*.jks
*.keystore
.idea/
captures/
```

Secrets: never in `build.gradle.kts`, `gradle.properties` committed to the repo, `BuildConfig` or resources — anything in the APK can be extracted. `local.properties` and signing keystores stay out of the repository; server-side secrets stay on the server.

## 7. Agent limits

JVM unit tests run where a JDK and the Android SDK are present — often not in a sandbox (`doctor` shows it). Emulators and devices are rarely available: instrumented tests and on-device checks are recorded as "not verified here — run `<command>` on a machine with the SDK and an emulator", and the slice is green only after the user reports the result. Play Store signing and release are always the user's.
