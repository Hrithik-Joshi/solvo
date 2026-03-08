# StudyAI — Full Jetpack Compose App

AI-powered homework solver for Android, built with Jetpack Compose.

---

## Project Structure

```
com.studyai.app/
│
├── MainActivity.kt                        ← Entry point, theme toggle
│
├── data/
│   └── remote/
│       └── ClaudeRepository.kt           ← All Claude API calls
│
├── domain/
│   └── model/
│       └── Models.kt                     ← Data classes (Subject, Solution, ChatMessage, QuizQuestion…)
│
└── ui/
    ├── navigation/
    │   └── Navigation.kt                 ← NavHost, Routes, AppState
    │
    ├── theme/
    │   ├── Color.kt                      ← All color tokens (dark + light)
    │   ├── Theme.kt                      ← MaterialTheme setup
    │   └── Type.kt                       ← Typography scale
    │
    ├── components/
    │   └── Components.kt                 ← Shared composables (GradientButton, SubjectChip, TypingIndicator…)
    │
    └── screens/
        ├── home/
        │   └── HomeScreen.kt             ← HomeViewModel + HomeScreen
        ├── solution/
        │   └── SolutionScreen.kt         ← Step-by-step solution with collapsible cards
        ├── chat/
        │   └── ChatScreen.kt             ← ChatViewModel + real-time AI chat
        └── quiz/
            └── QuizScreen.kt             ← QuizViewModel + Setup → Quiz → Results flow
```

---

## Setup Instructions

### 1. Create Android Project

In Android Studio:
- File → New → New Project
- Select **Empty Activity** (Compose template)
- Package: `com.studyai.app`
- Min SDK: 26
- Language: Kotlin

### 2. Copy files

Copy each `.kt` file into its matching package under `app/src/main/java/com/studyai/app/`.

Copy `build.gradle.kts` contents into your `app/build.gradle.kts`.

Copy the `AndroidManifest.xml` content into your manifest.

### 3. Add your API key

In `MainActivity.kt`, replace:
```kotlin
private val API_KEY = "YOUR_CLAUDE_API_KEY_HERE"
```
with your actual Claude API key from console.anthropic.com.

> ⚠️ **Security**: For Play Store release, move the key to a backend server.
> Never ship API keys in a production APK.

### 4. Add theme to res/values/themes.xml

```xml
<style name="Theme.StudyAI" parent="android:Theme.Material.NoTitleBar"/>
```

### 5. Sync & Run

Click **Sync Now** in Android Studio, then run on a device or emulator (API 26+).

---

## Features Implemented

| Screen | Features |
|--------|---------|
| **Home** | Subject selector chips, text input, Solve button, scan/image buttons, stats row, recent history |
| **Solution** | Collapsible step cards, final answer pill, links to Chat + Quiz |
| **Chat** | Full conversation history, typing indicator, suggestion chips, scrolls to latest |
| **Quiz** | Subject + difficulty setup, AI-generated MCQ, progress bar, detailed results with explanations |

### All Screens
- Dark & Light mode with toggle button
- Navigation with back stack (Navigation Compose)
- Real Claude API integration with coroutines
- Error handling with user-visible messages
- Loading states on all async operations

---

## Architecture

```
UI Layer      → Compose Screens + ViewModels (StateFlow)
Domain Layer  → Models + use case logic inside ViewModels
Data Layer    → ClaudeRepository (HttpURLConnection, no extra deps)
```

**Shared state** flows through `AppState` (passed by reference between screens) for the current question, solution text, and subject name.

---

## Extending the App

### Add Notes Generator (Pro feature)
```kotlin
// Already in ClaudeRepository:
val notes = appState.repo.generateNotes(topic, subject)
```
Build a `NotesScreen.kt` following the same pattern as `SolutionScreen`.

### Add Room history database
```kotlin
// 1. Add to build.gradle.kts:
implementation("androidx.room:room-runtime:2.6.1")
implementation("androidx.room:room-ktx:2.6.1")

// 2. Create HistoryDao, AppDatabase, and inject via Hilt
```

### Add Hilt DI
Replace `remember { HomeViewModel(appState) }` with `hiltViewModel()` after adding Hilt setup.

### Add Google Play Billing
```kotlin
implementation("com.android.billingclient:billing-ktx:7.0.0")
// See BillingClient documentation for subscription flow
```

---

## Dependencies Summary

| Library | Purpose |
|---------|---------|
| Jetpack Compose BOM 2024.06 | UI framework |
| Navigation Compose 2.8 | Screen routing |
| Lifecycle + ViewModel | State management |
| Kotlinx Coroutines | Async API calls |
| Kotlinx Serialization | JSON parsing |
| CameraX | Scan homework (UI hook ready) |
| ML Kit Text Recognition | OCR from camera |
| DataStore Preferences | User prefs, free tier counter |
