# Gemini API Compose Starter App (MAD Lab Assignment 1)

A modern, responsive Android application built with Jetpack Compose, Kotlin Coroutines, Room Database, Preferences DataStore, Android Keystore (AES-256-GCM encryption), and the Google Gemini Generative AI SDK (`gemini-3.6-flash`).

---

## 1. API Key Setup & Security Flow

### Where to Place the Key
To keep your API key secure and prevent accidental leaks into version control (VCS), the project reads the Gemini API key from a `local.properties` file located in the root project directory.

1. Copy `local.properties.example` to `local.properties` in the root folder of the project (`/Users/shravanishinde/Desktop/mad ass 1/local.properties`).
2. Add your Gemini API key as follows:
   ```properties
   GEMINI_API_KEY=your_actual_gemini_api_key_here
   ```
3. `local.properties` is strictly git-ignored (`.gitignore`), ensuring your credentials never land in Git history or public repositories.

### Environment Variable Fallback (CI/CD)
The build system (`app/build.gradle.kts`) automatically falls back to the `GEMINI_API_KEY` environment variable if `local.properties` is absent (ideal for CI/CD pipelines).

### Android Keystore AES-256-GCM Encryption Flow
- **Encryption at Rest**: Sensitive data and secure tokens (such as API keys) are encrypted using **AES-256-GCM** backed by the hardware-backed **Android Keystore system** (`KeystoreCrypto`).
- **In-Memory Decryption**: Ciphertext (combined with a cryptographic IV) is stored securely in Preferences DataStore and decrypted into memory only when needed.
- **Security Guarantee**: The key is never logged, printed, or exposed in plaintext.

---

## 2. Implemented Features & Architecture

- **Gemini AI Integration**: Uses `gemini-3.6-flash` via REST API with `HttpURLConnection` off the main thread (`Dispatchers.IO`).
- **LazyColumn & Chat Bubbles**: Conversations are rendered inside a high-performance `LazyColumn` using stable keys (`message.id`), paired with auto-scrolling.
- **State Hoisting**: All UI state resides in immutable `ChatUiState`, exposed as a `StateFlow` from `ChatViewModel`, collected safely via `collectAsStateWithLifecycle()`.
- **Responsive Layouts**: Utilizes `WindowSizeClass` and adaptive modifiers to adapt seamlessly across phones, tablets, and orientation changes.
- **Voice Input (Speech-to-Text)**: Allows users to input queries via voice using `RecognizerIntent`.
- **Persistent Storage**:
  - **Room Database**: Stores chat history so conversations survive app restarts.
  - **Preferences DataStore**: Manages user preferences and encrypted storage.
- **Loading & Error States**: Displays a `CircularProgressIndicator` during network calls and a Material 3 `Snackbar` when API calls fail.
- **Release R8 Minification**: Enabled `isMinifyEnabled = true` in `app/build.gradle.kts` for production builds with ProGuard optimization rules (`proguard-rules.pro`).

---

## 3. How to Run Tests

### Unit Tests
To run unit tests for `ChatViewModel` (using `kotlinx-coroutines-test` and fake repositories):
```bash
./gradlew testDebugUnitTest
```

### Instrumented & Compose UI Tests
To run Jetpack Compose UI tests (using `createAndroidComposeRule`):
```bash
./gradlew connectedAndroidTest
```
*(Requires a connected Android device or emulator)*

---

## 4. Limitations & Guidelines
- Requires an active internet connection to communicate with the Gemini API.
- Free-tier API limits apply (handle 429 Too Many Requests gracefully).
