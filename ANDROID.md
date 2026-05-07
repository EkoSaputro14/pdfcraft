# Android (Tauri Mobile) Guide

This repository includes Tauri v2 Android scaffolding in `src-tauri/gen/android` so you can run PDFCraft as a local Android app without adding a backend server.

## Prerequisites

- Node.js 18.17+
- npm
- Rust stable toolchain
- Android Studio
- Android SDK Platform 36
- Android SDK Build-Tools
- Android NDK (the version bundled by Android Studio is fine)
- Java 17 (Android Studio JBR also works)
- Tauri CLI v2 (`npm install`)

Install the Rust Android targets if they are not already available:

```bash
rustup target add aarch64-linux-android armv7-linux-androideabi i686-linux-android x86_64-linux-android
```

Make sure Android Studio has accepted licenses and that `ANDROID_HOME` or `ANDROID_SDK_ROOT` points to your SDK directory when needed by your shell.

## First-time setup

From the repository root:

```bash
npm install
npm run android:init
```

`android:init` is only needed again if you want Tauri to regenerate the Android project after changing the app identifier or other native metadata.

Use the `npm run android:*` commands for development and packaging. Tauri generates helper files such as `tauri.settings.gradle` during its own Android workflow, so calling `./gradlew` directly from `src-tauri/gen/android` can fail before a Tauri command has prepared the project.

## Run on an emulator or device

1. Start an Android emulator from Android Studio **or** connect a USB-debuggable device.
2. Verify the device is visible:

   ```bash
   adb devices
   ```

3. Start the Android development build:

   ```bash
   npm run android:dev
   ```

Tauri will build the Next.js frontend, compile the Android project, install the debug app, and launch it on the selected device/emulator.

## Build release artifacts

Create release APK/AAB outputs with:

```bash
npm run android:build
```

Common output locations:

- APK: `src-tauri/gen/android/app/build/outputs/apk/`
- AAB: `src-tauri/gen/android/app/build/outputs/bundle/`

If you need to test the production bundle on a device without generating release artifacts, use:

```bash
npm run android:run
```

## Desktop builds

Desktop Tauri workflows are unchanged:

```bash
npm run dev:tauri
npm run build:tauri
```

## Troubleshooting

### `SDK location not found`

- Open Android Studio once and install the SDK components.
- Set `ANDROID_HOME` or `ANDROID_SDK_ROOT` to the SDK path.
- If needed, create `src-tauri/gen/android/local.properties` with `sdk.dir=/absolute/path/to/Android/sdk`.

### `NDK not configured` or Rust Android link errors

- Install the Android NDK from Android Studio's SDK Manager.
- Re-run `rustup target add` for the Android targets listed above.
- Re-run `npm run android:init` if you changed SDK/NDK locations.

### Gradle daemon or dependency resolution problems

- From `src-tauri/gen/android`, run `./gradlew --stop` and retry.
- Delete `src-tauri/gen/android/.gradle` if caches became stale.
- Confirm your Java version is 17:

  ```bash
  java -version
  ```

### `tauri.settings.gradle` missing

- Run `npm run android:dev` or `npm run android:build` instead of invoking Gradle directly first.
- If you want to reopen the native project in Android Studio, re-run `npm run android:init` and then use the Tauri Android commands so the helper files are regenerated.

### Frontend build fails during Android packaging

Android packaging uses the same Next.js export as desktop Tauri. If `npm run build` fails, fix that first because `npm run android:build` depends on the same frontend build step.
