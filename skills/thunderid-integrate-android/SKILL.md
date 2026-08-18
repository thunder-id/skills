---
name: thunderid-integrate-android
description: Add ThunderID authentication to an Android (Jetpack Compose) application using the official ThunderID Android SDK. Use when asked to "integrate ThunderID into Android", "add auth to my Android app", or "connect ThunderID with Jetpack Compose".
license: Apache-2.0
allowed-tools: WebFetch, Bash(./gradlew:*), Bash(gradle:*), Read, Write, Edit
metadata:
  author: thunderid
  version: 0.0.3
---

# ThunderID — Android Integration

Assumes ThunderID is running at `https://localhost:8090`. If not, run `/thunderid-install` first.

Fetch the canonical, always-current integration guide before writing any code:

WebFetch `https://thunderid.dev/docs/v1.0.x/getting-started/connect-your-application/android.md`

Follow that guide end to end — creating the Console application, adding the Gradle dependency, wrapping the app with the ThunderID composable provider, adding sign-in/sign-out UI, and verifying the app runs on an emulator or device — adapting file names and code style to this project's actual structure rather than copying the doc's file layout verbatim.

You do not have Console access or a browser tool. Whenever a step needs a Console UI action (creating the application, copying the Client ID, adding a redirect URI), tell the user exactly what to click and ask them to report the value back.

If the fetch fails (offline, docs unreachable), tell the user and give them the URL above to open manually. Do not improvise integration steps from memory.

## Project-specific notes

- A local ThunderID instance uses a self-signed certificate; the guide's network security config for trusting the local cert during development must be applied, or sign-in requests will fail.
- Confirm whether the project uses Gradle Groovy (`build.gradle`) or Kotlin DSL (`build.gradle.kts`) before editing, and match the existing style.

## Troubleshooting

- **Certificate / TLS error talking to `https://localhost:8090`** — Confirm the app's network security config from the guide trusts the local cert; this is the most common local-development failure on Android.
- **10.0.2.2 vs localhost** — On the Android emulator, `localhost` refers to the emulator itself, not the host machine; the guide's base URL configuration should account for this if targeting the emulator.
