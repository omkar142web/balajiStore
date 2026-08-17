# Capacitor Guide

This guide explains how to use [Capacitor](https://capacitorjs.com/) to turn a web app into a native iOS/Android app.

Capacitor lets you take an existing web project (HTML/JS or a framework like React/Vue/Angular) and wrap it in a native shell so it can be distributed through the App Store / Play Store, while still using web technology.

---

## 1. Prerequisites

Your project must have these 3 things:

- A `package.json` file
- A separate folder for built web assets (e.g. `dist`, `www`, `build`)
- An `index.html` file at the root of that assets folder

> **Important:** `index.html` MUST contain a `<head>` tag. Capacitor injects its bridge there. Without `<head>`, plugins will not work.

Example: this repo is a single-page app with `index.html` at the root, so `webDir` will be `"."`.

You also need the native toolchains installed and on your PATH:

| Platform | Requirements |
| --- | --- |
| Android | Node.js 18+, JDK 17, Android Studio (with Android SDK) |
| iOS (macOS only) | Node.js 18+, Xcode, CocoaPods |

Check your environment before building for a platform:

```
npx cap doctor
```

---

## 2. Install Capacitor

Install the core runtime and the CLI:

```
npm i @capacitor/core
npm i -D @capacitor/cli
```

---

## 3. Initialize the config

```
npx cap init
```

The CLI asks for:

- **App name** — the display name of your app
- **Package ID** — reverse-domain identifier, e.g. `com.example.backtickescaper`

This creates a `capacitor.config.json` (or `.ts`/`.js`) file with your config, including `webDir` (the folder where your built web assets live).

Example `capacitor.config.json`:

```json
{
  "appId": "com.example.backtickescaper",
  "appName": "Backtick Escaper",
  "webDir": "."
}
```

> The CLI tries to auto-detect `webDir` from your framework (Angular `www`, React `build`, Vue `dist`, Vite `dist`). Double-check it if syncing has issues.

---

## 4. Add platforms

```
npm i @capacitor/android @capacitor/ios
npx cap add android
npx cap add ios
```

This creates an `android/` and `ios/` folder containing native projects. (You only need the platforms you plan to build for.)

---

## 5. Build your web app

Capacitor copies your **built** web assets into the native project, so build first:

```
# for plain HTML apps like this one, nothing to build — your index.html is the output
npm run build   # if your project has a build step (React/Vue/Angular/etc.)
```

---

## 6. Sync web code to native projects

```
npx cap sync
```

This:
1. Copies your web bundle from `webDir` into the native projects
2. Installs/updates native dependencies (CocoaPods on iOS, Gradle on Android)
3. Installs any Capacitor plugins

Use `npx cap copy` if you only want to copy the web assets without touching native dependencies.

---

## 7. Run the app

**Android** — opens Android Studio:

```
npx cap open android
```

Then click **Run** (green play button) in Android Studio, or build the APK there.

**iOS** (macOS only) — opens Xcode:

```
npx cap open ios
```

Then select a simulator/device and hit Run.

**Web** — test your app in the browser during development:

```
npm start
```

---

## 8. Using plugins

Plugins add native functionality (camera, geolocation, notifications, etc.). Install with npm, then sync:

```
npm i @capacitor/camera
npx cap sync
```

Then use the JavaScript API in your web code:

```js
import { Camera, CameraResultType } from '@capacitor/camera';

const photo = await Camera.getPhoto({
  resultType: CameraResultType.Uri
});
```

Common official plugins:

- `@capacitor/camera`
- `@capacitor/geolocation`
- `@capacitor/splash-screen`
- `@capacitor/push-notifications`
- `@capacitor/storage`
- `@capacitor/network`

---

## 9. Adding Capacitor to a web app recap

```bash
npm i @capacitor/core
npm i -D @capacitor/cli
npx cap init
npm i @capacitor/android @capacitor/ios
npx cap add android
npx cap add ios
# build your web app first, then:
npx cap sync
npx cap open android   # or: npx cap open ios
```

---

## Common commands cheat-sheet

| Command | Purpose |
| --- | --- |
| `npx cap init` | Create config file (`capacitor.config.*`) |
| `npx cap add <platform>` | Add `android` / `ios` native project |
| `npx cap sync` | Copy web assets + install native deps/plugins |
| `npx cap copy` | Copy web assets only |
| `npx cap update` | Update native deps/plugins only |
| `npx cap open <platform>` | Open the native project in Android Studio / Xcode |
| `npx cap run <platform>` | Build + deploy to device/simulator |
| `npx cap doctor` | Check your environment is ready |
| `npx cap ls` | List installed platforms and plugins |

---

## Useful docs

- Install: https://capacitorjs.com/docs/getting-started
- Config reference: https://capacitorjs.com/docs/config
- Plugins: https://capacitorjs.com/docs/apis
- Upgrade guides: https://capacitorjs.com/docs/updating




<!-- 
For your specific project (this is a plain HTML app with index.html at the root), to get it running on Android:
npm i @capacitor/core
npm i -D @capacitor/cli
npx cap init
npm i @capacitor/android
npx cap add android
npx cap sync
npx cap open android
Note: your index.html already has a <head> tag (required by Capacitor), and since it's plain HTML, set webDir to "." in your config (or the CLI will likely detect it). Requires Android Studio + JDK 17 installed on your machine. 
-->