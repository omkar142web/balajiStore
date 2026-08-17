<!--
 Here are the commands to run from the project root (tryone):

npx cap sync
cd android
.\gradlew.bat assembleDebug

The APK will be at android\app\build\outputs\apk\debug\app-debug.apk.
To install it on a connected device (USB debugging on), run after building:
.\gradlew.bat installDebug
Wait — one check first: did you edit www/index.html (not android/app/src/main/assets/public/index.html)? If you edited the copied one, cap sync will overwrite your changes. 

-->

# Daily Cycle — How to Update This App

Use this every time you change the app's HTML.

## The cycle

```
1. Edit   →  edit www/index.html  (the "source of truth")
2. Sync   →  npx cap sync         (copy into the Android project)
3. Build  →  .\gradlew.bat assembleDebug   (in android/)
4. Install→  .\gradlew.bat installDebug     (in android/, device connected)
```

## Step-by-step

### 1. Edit the code
- Open and edit `www/index.html` only.
- Keep the `<head>` tag — Capacitor needs it for plugins to work.
- If the app name changes, also edit `appName` in `capacitor.config.json`.

### 2. Sync to the native project
Run from the project root:

```
npx cap sync
```

This copies `www/index.html` into `android/app/src/main/assets/public/index.html`.

> Do NOT edit `android/app/src/main/assets/public/index.html` directly — it gets overwritten on every sync.

### 3. Build the APK
Run from inside the `android/` folder:

```
cd android
.\gradlew.bat assembleDebug
```

New APK: `android\app\build\outputs\apk\debug\app-debug.apk`

### 4. Install on your device
With the phone connected via USB and USB debugging enabled:

```
.\gradlew.bat installDebug
```

---

## Cheat-sheet

| Action | Command |
| --- | --- |
| Edit app code | `www/index.html` |
| Sync web → native | `npx cap sync` |
| Build debug APK | `.\gradlew.bat assembleDebug` |
| Install on device | `.\gradlew.bat installDebug` |
| Open in Android Studio | `npx cap open android` |

That's it — edit, sync, build, install.