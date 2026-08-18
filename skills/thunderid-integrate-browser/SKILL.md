---
name: thunderid-integrate-browser
description: Add ThunderID authentication to a framework-free (vanilla) JavaScript application using the official @thunderid/browser SDK. Use when asked to "integrate ThunderID into JavaScript", "integrate ThunderID into vanilla JS", "add auth to my browser app", or "connect ThunderID with plain JavaScript".
license: Apache-2.0
allowed-tools: WebFetch, Bash(npm:*), Bash(npx:*), Bash(pnpm:*), Bash(yarn:*), Bash(bun:*), Read, Write, Edit
metadata:
  author: thunderid
  version: 1.0.0
---

# ThunderID — JavaScript (Browser) Integration

Assumes ThunderID is running at `https://localhost:8090`. If not, run `/thunderid-install` first.

Fetch the canonical, always-current integration guide before writing any code:

WebFetch `https://thunderid.dev/docs/v1.0.x/getting-started/connect-your-application/browser.md`

Follow that guide end to end — creating the Console application, installing `@thunderid/browser`, initializing `ThunderIDBrowserClient`, adding sign-in/sign-out UI, and verifying the app runs — adapting file names, paths, and code style to this project's actual structure rather than copying the doc's file layout verbatim.

You do not have Console access or a browser tool. Whenever a step needs a Console UI action (creating the application, copying the Client ID, adding a redirect URI), tell the user exactly what to click and ask them to report the value back.

If the fetch fails (offline, docs unreachable), tell the user and give them the URL above to open manually. Do not improvise integration steps from memory.

## Project-specific notes

- Detect the package manager from lockfiles: `pnpm-lock.yaml` → `pnpm add`, `yarn.lock` → `yarn add`, `bun.lockb` → `bun add`, else `npm install`.

## Troubleshooting

- **Certificate error** — Visit `https://localhost:8090` in your browser and accept the warning once.
- **`redirect_uri_mismatch`** — The dev server's origin must exactly match an entry in **Authorized Redirect URIs** on the application.
- **Blank page / stuck on redirect** — The client's redirect-completion call must run before checking sign-in state; it's what completes the OAuth code exchange after the redirect back from ThunderID.
