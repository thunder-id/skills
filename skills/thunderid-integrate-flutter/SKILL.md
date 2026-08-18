---
name: thunderid-integrate-flutter
description: Add ThunderID authentication to a Flutter application using the official thunderid_flutter package. Use when asked to "integrate ThunderID into Flutter", "add auth to my Flutter app", or "connect ThunderID with Flutter".
license: Apache-2.0
allowed-tools: WebFetch, Bash(flutter:*), Bash(dart:*), Bash(pub:*), Read, Write, Edit
metadata:
  author: thunderid
  version: 1.0.0
---

# ThunderID — Flutter Integration

Assumes ThunderID is running at `https://localhost:8090`. If not, run `/thunderid-install` first.

Fetch the canonical, always-current integration guide before writing any code:

WebFetch `https://thunderid.dev/docs/v1.0.x/getting-started/connect-your-application/flutter.md`

Follow that guide end to end — creating the Console application, adding the `thunderid_flutter` package, initializing the SDK, adding sign-in/sign-out widgets, displaying the signed-in user, and verifying the app runs on a simulator/emulator or device — adapting file names and code style to this project's actual structure rather than copying the doc's file layout verbatim.

You do not have Console access or a browser tool. Whenever a step needs a Console UI action (creating the application, copying the Client ID, adding a redirect URI), tell the user exactly what to click and ask them to report the value back.

If the fetch fails (offline, docs unreachable), tell the user and give them the URL above to open manually. Do not improvise integration steps from memory.

## Project-specific notes

- A local ThunderID instance uses a self-signed certificate; the guide's platform-specific cert-trust setup (iOS ATS / Android network security config, as applicable) must be applied for both target platforms this app builds for, or sign-in requests will fail.
- On the Android emulator, `localhost` refers to the emulator itself, not the host machine — check whether the guide's base URL needs `10.0.2.2` instead when targeting the emulator.

## Troubleshooting

- **Certificate / TLS error talking to `https://localhost:8090`** — Confirm the platform-specific cert-trust configuration from the guide is in place for whichever platform (iOS/Android) is failing; this is the most common local-development failure.
