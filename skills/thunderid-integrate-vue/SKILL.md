---
name: thunderid-integrate-vue
description: Add ThunderID authentication to a Vue application using the official @thunderid/vue SDK. Use when asked to "integrate ThunderID into Vue", "add auth to my Vue app", or "connect ThunderID with Vue".
license: Apache-2.0
allowed-tools: WebFetch, Bash(npm:*), Bash(npx:*), Bash(pnpm:*), Bash(yarn:*), Bash(bun:*), Read, Write, Edit
metadata:
  author: thunderid
  version: 0.0.2
---

# ThunderID — Vue Integration

Assumes ThunderID is running at `https://localhost:8090`. If not, run `/thunderid-install` first.

Fetch the canonical, always-current integration guide before writing any code:

WebFetch `https://thunderid.dev/docs/v1.0.x/getting-started/connect-your-application/vue.md`

Follow that guide end to end — creating the Console application, installing `@thunderid/vue`, registering the plugin, adding sign-in/sign-out UI, and verifying the app runs — adapting file names, paths, and code style to this project's actual structure rather than copying the doc's file layout verbatim.

You do not have Console access or a browser tool. Whenever a step needs a Console UI action (creating the application, copying the Client ID, adding a redirect URI), tell the user exactly what to click and ask them to report the value back. If the guide presents more than one configuration approach, use the `AskUserQuestion` tool to let the user choose rather than guessing.

If the fetch fails (offline, docs unreachable), tell the user and give them the URL above to open manually. Do not improvise integration steps from memory.

## Project-specific notes

- Detect the package manager from lockfiles: `pnpm-lock.yaml` → `pnpm add`, `yarn.lock` → `yarn add`, `bun.lockb` → `bun add`, else `npm install`.

## Troubleshooting

- **Certificate error** — Visit `https://localhost:8090` in your browser and accept the warning once.
- **Composables fail outside `setup()`** — `useThunderID()` and `useUser()` use Vue's `inject` and must be called inside a component's `setup()`.
- **`redirect_uri_mismatch`** — The dev server's origin must exactly match an entry in **Authorized Redirect URIs** on the application.
