# J.A.R.V.I.S AI — Android Studio project

**Developer:** KARTHIKEYAN  
**Footer identity:** BUILT BY KARTHIK  
**Application ID:** `com.karthik.jarvisai`

This is a native Kotlin + Jetpack Compose Android project, not a website wrapper. It includes a futuristic dark/cyan UI using the supplied portrait image, Android speech recognition, Android TextToSpeech, a local command router, contact lookup that opens the native dialer for user confirmation, app/settings intents, local notes, battery/network/time information, and an opt-in notification listener for selected messaging app package names.

## Important status / honesty

This source project has **not been compiled or device-tested in this environment** because the Android SDK and Gradle executable are not installed here. Therefore no APK is being represented as built or tested. Open it in a current Android Studio installation, let Gradle sync, then build and test on a real device before installing beyond your own test phone.

This is a functional starter implementation, not yet the fully verified production app described in the request. In particular:

- Incoming call announcement is **not implemented** in this version. Android restrictions around caller ID, background execution, and call-state permissions vary by OS/OEM. Implement and test it on-device before advertising it as supported.
- There is no always-listening microphone. Voice input begins only when the user taps the microphone.
- The notification listener can read only content actually exposed in notifications by Android. Hidden previews cannot be recovered. Default selected package IDs are WhatsApp and common SMS apps; app selection UI and quiet-hour/lock-screen controls are not yet exposed in the UI (preferences hooks exist for quiet hours and locked-screen behavior).
- It does not read WhatsApp's private chat database. It uses authorized notification previews only.
- Direct silent mode, Wi-Fi/mobile data control, unrestricted call log access, arbitrary background phone control, and silently sending messages are not provided. Android settings/intents and user confirmation are used instead.
- Weather opens a web search; no weather API has been configured.
- The AI backend answers text questions only. It cannot control Android, and it receives only the message explicitly sent to `/chat`.
- App opening is currently wired for WhatsApp, YouTube and Chrome examples; arbitrary installed-app resolution needs a user-facing app picker and should be added before release.

## Requirements

- Android Studio with Android SDK platform 36 and JDK 17.
- Internet for Gradle dependencies and (optionally) AI backend.
- Android 8.0 (API 26) or newer. Notification permission prompts vary with Android version; notification listener access must be enabled manually in Settings.

## Build APK

1. Extract the ZIP.
2. Open the `JarvisAI` folder in Android Studio.
3. Wait for Gradle sync and install any requested SDK platform/build tools.
4. Choose **Build → Build Bundle(s) / APK(s) → Build APK(s)**.
5. Android Studio will show the actual APK output path, usually `app/build/outputs/apk/debug/app-debug.apk`.
6. Install the debug APK on your own phone and test every permission flow. A release APK requires your own signing key under **Build → Generate Signed Bundle / APK**. Keep the keystore private.

## First-run setup

1. Open the app and tap the microphone. Grant microphone permission only if you want voice commands. Android's installed recognition provider controls supported languages; try Tamil/English/Thanglish and verify the recognition results on your phone.
2. Enable notification announcements using the switch, then enable **J.A.R.V.I.S message announcements** in Android's Notification access settings. Notification access is powerful: grant it only if you accept the privacy implications.
3. TextToSpeech voice/language availability depends on installed Android TTS engines and language data.
4. Contact search asks for `READ_CONTACTS`; if granted, it searches a matching name and opens the native dialer for you to review and call.

## Gemini backend (API key stays on the server)

The Android app intentionally does not embed a Gemini API key. Run the included Node backend on a trusted HTTPS host or your own development computer.

```bash
cd backend
npm install
# Linux/macOS shell example; use your shell's equivalent on Windows:
export GEMINI_API_KEY='YOUR_KEY_FROM_GOOGLE_AI_STUDIO'
export GEMINI_MODEL='gemini-3.8-flash'
npm start
```

In the app, enter your backend base URL (for example `https://your-host.example`) in the **Secure AI Backend URL** field and tap **Save Backend URL**. Do not publish a server URL that has no authentication/rate limits. The included sample has basic rate limiting and sends only the explicit chat message, but production deployment still needs authentication, HTTPS, abuse monitoring, quotas, and a privacy notice.

Official Gemini API documentation: https://ai.google.dev/gemini-api/docs  
The included sample uses Google's official `@google/genai` JavaScript SDK and the Interactions API, which Google currently recommends for new projects. Confirm that the selected model is available to your API account before deployment.

## Notification behavior

Android exposes notification title/text fields differently by app and privacy state. A sender/title is not always a human contact name; the app must not infer one. The listener skips ongoing notifications, suppresses duplicate notification keys while running, reads only selected package IDs, and honors saved opt-in/quiet-hour/locked-screen preference values. Quiet-hour and locked-screen settings still need proper user controls in the app before production use.

## Security and release checklist

- Add user-facing settings for selected messaging apps, quiet hours, locked-screen announcements, backend URL, history deletion, and AI consent.
- Implement call announcements only after validating current Android APIs, permission requirements, Play policy, and OEM behavior on real devices.
- Add proper HTTPS backend authentication, server-side request quotas, privacy policy, and secure storage for user settings.
- Test permission denial/revocation, speech provider absence, Bluetooth/headphone audio, notification preview hiding, Do Not Disturb, battery restrictions, reboot, Android 8 through current versions, and duplicate event behavior.
- Do not claim call announcements, release signing, or device testing until those tasks have actually been completed.
