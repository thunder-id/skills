---
name: thunderid-integrate-ios
description: Add ThunderID authentication to an iOS (SwiftUI) application using the official ThunderID iOS SDK. Use when asked to "integrate ThunderID into iOS", "add auth to my iOS app", or "connect ThunderID with SwiftUI".
license: Apache-2.0
allowed-tools: WebFetch, Bash(pod:*), Bash(swift:*), Bash(xcodebuild:*), Read, Write, Edit
metadata:
  author: thunderid
  version: 1.0.0
---

# ThunderID — iOS Integration

Assumes ThunderID is running at `https://localhost:8090`. If not, run `/thunderid-install` first.

Fetch the canonical, always-current integration guide before writing any code:

WebFetch `https://thunderid.dev/docs/v1.0.x/getting-started/connect-your-application/ios.md`

Follow that guide end to end — creating the Console application, adding the SDK via Swift Package Manager, injecting the ThunderID environment object, adding sign-in/sign-out UI, and verifying the app runs on a simulator or device — adapting file names and code style to this project's actual structure rather than copying the doc's file layout verbatim.

You do not have Console access or a browser/Xcode GUI tool. Whenever a step needs a Console UI action (creating the application, copying the Client ID, adding a redirect URI) or an Xcode GUI action (adding a Swift Package via File > Add Package Dependencies), tell the user exactly what to click and ask them to report back or confirm once done.

If the fetch fails (offline, docs unreachable), tell the user and give them the URL above to open manually. Do not improvise integration steps from memory.

## Project-specific notes

- A local ThunderID instance uses a self-signed certificate; the guide's setup for App Transport Security / trusting the local cert during development must be applied, or sign-in requests will fail silently.

## Troubleshooting

- **Certificate / TLS error talking to `https://localhost:8090`** — Confirm the app's ATS exception or cert-trust configuration from the guide is in place; this is the most common local-development failure on iOS.
