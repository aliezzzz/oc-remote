# AGENTS.md

Guidelines for agentic coding agents working on the OC Remote Android codebase.

## Project Overview

OC Remote is an Android client for OpenCode servers written in Kotlin using Jetpack Compose and Material 3. Uses MVVM architecture with Hilt DI.

## Build Commands

```bash
# Build debug APK
./gradlew assembleDebug

# Build release APK (requires keystore setup)
./gradlew assembleRelease

# Clean build
./gradlew clean

# Install on connected device
adb install -r app/build/outputs/apk/debug/app-debug.apk
```

## Test Commands

```bash
# Run unit tests
./gradlew test

# Run instrumented tests (requires connected device/emulator)
./gradlew connectedAndroidTest

# Run single test class
./gradlew test --tests "ClassName"

# Run single test method
./gradlew test --tests "ClassName.methodName"
```

Note: The project has test dependencies configured but minimal test coverage currently.

## Code Style

### Kotlin Style
- Follows official Kotlin code style (`kotlin.code.style=official` in gradle.properties)
- 4-space indentation
- Use trailing commas in multi-line parameter lists
- Explicit type declarations on public APIs

### Naming Conventions
- Classes: PascalCase (e.g., `ServerRepository`, `HomeViewModel`)
- Functions/properties: camelCase (e.g., `getServerConfig`, `isConnected`)
- Constants: UPPER_SNAKE_CASE (e.g., `LANGUAGE_KEY`, `DEFAULT_TIMEOUT`)
- XML resources: snake_case (e.g., `ic_server_add`, `screen_home`)
- Composables: PascalCase (e.g., `HomeScreen`, `ChatMessageCard`)

### Imports
- Group imports: Android/Kotlin stdlib first, then third-party, then project
- Use wildcard imports only for compose (`androidx.compose.*`)
- No star imports for other packages

### Architecture Patterns

**Repository Pattern:**
```kotlin
@Singleton
class ServerRepository @Inject constructor(
    private val apiClient: ApiClient
) { ... }
```

**ViewModel with StateFlow:**
```kotlin
@HiltViewModel
class HomeViewModel @Inject constructor(
    private val repository: ServerRepository
) : ViewModel() {
    private val _uiState = MutableStateFlow(HomeUiState())
    val uiState: StateFlow<HomeUiState> = _uiState.asStateFlow()
}
```

**Compose UI:**
```kotlin
@Composable
fun HomeScreen(
    viewModel: HomeViewModel = hiltViewModel(),
    onNavigateToChat: (String) -> Unit
) { ... }
```

### Error Handling
- Use `Result<T>` for operations that can fail
- Use try/catch with specific exception types
- Propagate errors via Flow to UI layer
- Log errors with appropriate tags: `Log.e(TAG, "message", exception)`

### Documentation
- Use KDoc for public classes and methods
- Document complex logic with inline comments
- Include parameter descriptions for non-obvious parameters

## Localization

- Manage translations with `lokit` tool (see `lokit.yaml`)
- Source language: English (`en`)
- 15 supported locales: ru, de, es, fr, it, id, pt-BR, ja, ko, zh-CN, uk, tr, ar, pl
- String resources in `app/src/main/res/values-*/strings.xml`

## Key Technologies

- Kotlin 2.0.21
- Jetpack Compose (BOM 2024.12.01)
- Material 3 with dynamic colors
- Hilt for DI
- Ktor Client for networking
- DataStore for preferences
- kotlinx.serialization for JSON

## CI/CD

GitHub Actions workflow in `.github/workflows/release.yml`:
- Triggers on git tags `v*`
- Builds release APK with `./gradlew :app:assembleRelease`
- Creates GitHub release with APK attachment

## Dependencies

Key dependencies (see `app/build.gradle.kts`):
- AndroidX Core/Lifecycle
- Compose BOM 2024.12.01
- Navigation Compose
- Hilt 2.51
- Ktor Client 2.3.11
- Kotlinx Serialization
- Markdown Renderer
- DataStore
- Coil

Always check existing patterns in similar files when adding new code.
