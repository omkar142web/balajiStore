# How I Turned This HTML App Into an Android App (Capacitor)

This file documents exactly what was done to wrap the **Backtick Escaper** web app (`index.html`) with Capacitor and build an Android APK. Use it as a step-by-step recipe to repeat the process on any plain HTML project.

---

## What happened (session log)

| Step | Command / Action | Result |
| --- | --- | --- |
| 1. Check environment | `node --version`, `npm --version`, `java -version` | Node v24, npm 11, JDK 21 — all good |
| 2. Install Capacitor | `npm i @capacitor/core` and `npm i -D @capacitor/cli` | Capacitor 8.5.0 installed |
| 3. Init config | `npx cap init "Backtick Escaper" "com.example.backtickescaper" --web-dir "."` | Created `capacitor.config.json` |
| 4. Fix `webDir` | `"."` is NOT valid in Capacitor 8 → created `www/` and moved `index.html` in, set `"webDir": "www"` | Config valid |
| 5. Add platform | `npm i @capacitor/android` then `npx cap add android` | Created `android/` native project, copied web assets |
| 6. Sync | `npx cap sync` | Web assets copied to `android/app/src/main/assets/public` |
| 7. SDK pointer | Created `android/local.properties` with `sdk.dir=C:\Users\...\Android\Sdk` | Gradle could find the Android SDK |
| 8. Build | `.\gradlew.bat assembleDebug` (in `android/`) | BUILD SUCCESSFUL → `app-debug.apk` (3.9 MB) |

Final APK: `android\app\build\outputs\apk\debug\app-debug.apk`

---

## The recipe — repeat it on any HTML project

### 1. Environment requirements

- Node.js 18+
- For Android: JDK 17+ and Android Studio / Android SDK
- For iOS (macOS only): Xcode + CocoaPods

Verify first:

```
node --version
npm --version
java -version
```

> The Android SDK was found at `%LOCALAPPDATA%\Android\Sdk` even though `ANDROID_HOME` was not set. Gradle needs `local.properties` to know where it is (see step 7).

### 2. Project structure requirement

Your web app needs:

- A `package.json`
- A **dedicated folder** for web assets (Capacitor calls it `webDir`) — e.g. `www`, `dist`, `build`
- An `index.html` **with a `<head>` tag** inside that folder (plugins break without it)

> **Gotcha (this happened to me):** `webDir: "."` (the project root) is **not valid** in Capacitor 8 — the copy step fails. Plain HTML projects must move `index.html` into a subfolder:

```
mkdir www
move index.html www\index.html
```

### 3. Install Capacitor

```
npm i @capacitor/core
npm i -D @capacitor/cli
```

### 4. Initialize the config

```
npx cap init "Your App Name" "com.example.yourid" --web-dir "www"
```

Non-interactive form of the questionnaire. It creates `capacitor.config.json`:

```json
{
  "appId": "com.example.yourid",
  "appName": "Your App Name",
  "webDir": "www"
}
```

### 5. Add platforms

```
npm i @capacitor/android @capacitor/ios
npx cap add android
npx cap add ios
```

(Only add the platforms you actually need. This project used only Android.)

### 6. Sync web code

```
npx cap sync
```

Copies everything in `webDir` into `android/app/src/main/assets/public` and installs native deps/plugins.

### 7. Point Gradle at your Android SDK (if needed)

If `ANDROID_HOME` isn't set as an env var, Gradle can't find the SDK. Create `android/local.properties`:

```
sdk.dir=C\:\\Users\\YOURNAME\\AppData\\Local\\Android\\Sdk
```

(Escape the backslashes and colons like that — it's a Java properties file.)

### 8. Build the APK

```
cd android
.\gradlew.bat assembleDebug
```

Or via the CLI from the project root:

```
npx cap open android   # opens Android Studio, press Run
```

### 9. Install on a device/emulator

With USB debugging enabled and a device connected:

```
cd android
.\gradlew.bat installDebug
```

---

## Day-to-day workflow (after setup)

```
# 1. Edit your HTML/JS in www/
# 2. Rebuild web bundle if using a framework (React/Vue/Angular)
# 3. Push changes to the native project
npx cap sync
# 4. Reinstall on device
cd android
.\gradlew.bat installDebug
```

---

## Commands cheat-sheet

| Command | Purpose |
| --- | --- |
| `npm i @capacitor/core` + `npm i -D @capacitor/cli` | Install Capacitor |
| `npx cap init <name> <id> --web-dir <dir>` | Create config |
| `npm i @capacitor/android @capacitor/ios` | Install platform packages |
| `npx cap add android` / `npx cap add ios` | Add native project |
| `npx cap sync` | Copy web assets + install deps/plugins |
| `npx cap copy` | Copy web assets only |
| `npx cap open android` | Open Android Studio |
| `npx cap doctor` | Check environment readiness |
| `.\gradlew.bat assembleDebug` | Build debug APK (run inside `android/`) |
| `.\gradlew.bat installDebug` | Install APK on connected device |

---

## Key files in this project

```
tryone/
├── www/
│   └── index.html          ← your web app (the source of truth)
├── android/                ← native Android project (generated)
│   ├── local.properties    ← sdk.dir (hand-created)
│   └── app/src/main/assets/public/
│       └── index.html      ← copied by `cap sync` (do NOT edit here)
├── capacitor.config.json   ← app id, name, webDir
├── package.json
└── CAPACITOR_GUIDE.md
```

> Rule of thumb: edit code in `www/`, never touch `android/app/src/main/assets/public/` — it gets overwritten on every `cap sync`.