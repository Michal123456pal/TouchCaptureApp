# Touch Capture

Android app that opens the **front camera**, captures a photo **every time the screen is touched**, and uploads the image to **Firebase Storage** (your cloud).

## Features

- Front-facing camera only (CameraX)
- Full-screen preview
- Capture on any screen touch (with 800 ms cooldown to avoid spam)
- Automatic upload to Firebase Storage under `captures/`
- Anonymous Firebase Auth so Storage security rules can require authentication
- Minimal UI — status text at the bottom

## Requirements

- Android Studio Ladybug or newer (or AGP 8.7+)
- minSdk 24 / targetSdk 35
- A Firebase project with Storage enabled
- `google-services.json` from your Firebase console

## Setup

1. Create a Firebase project at https://console.firebase.google.com
2. Add an Android app with package name `com.nono.touchcapture`
3. Download `google-services.json` and place it in `app/`
4. In Firebase Console → Storage → Rules, use something like:

```
rules_version = '2';
service firebase.storage {
  match /b/{bucket}/o {
    match /captures/{fileName} {
      allow read, write: if request.auth != null;
    }
  }
}
```

5. Open the project in Android Studio, sync Gradle, run on a physical device (front camera required).

## Build

```bash
./gradlew assembleDebug
```

APK will be at `app/build/outputs/apk/debug/app-debug.apk`

## Project structure

```
app/
  src/main/
    java/com/nono/touchcapture/MainActivity.kt
    res/layout/activity_main.xml
    AndroidManifest.xml
  build.gradle.kts
google-services.json   ← you add this
```

## Notes

- Photos are written to the app cache then uploaded; successful uploads delete the local file.
- Cooldown prevents rapid-fire captures from flooding storage.
- Works offline for capture (files stay in cache); upload retries when network returns only if you keep the file (current code deletes on success only).

Made for Nono.
