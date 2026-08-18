---
name: thunderid-integrate-nuxt
description: Add ThunderID authentication to a Nuxt application using the official @thunderid/nuxt module. Use when asked to "integrate ThunderID into Nuxt", "add auth to my Nuxt app", or "connect ThunderID with Nuxt".
license: Apache-2.0
allowed-tools: WebFetch, Bash(npm:*), Bash(npx:*), Bash(pnpm:*), Bash(yarn:*), Bash(bun:*), Read, Write, Edit
metadata:
  author: thunderid
  version: 1.0.0
---

# ThunderID — Nuxt Integration

Assumes ThunderID is running at `https://localhost:8090`. If not, run `/thunderid-install` first.

Fetch the canonical, always-current integration guide before writing any code:

WebFetch `https://thunderid.dev/docs/v1.0.x/getting-started/connect-your-application/nuxt.md`

Follow that guide end to end — creating the Console application, installing `@thunderid/nuxt`, registering the module, adding sign-in/sign-up UI and page protection, and verifying the app runs — adapting file names, paths, and code style to this project's actual structure rather than copying the doc's file layout verbatim.

You do not have Console access or a browser tool. Whenever a step needs a Console UI action (creating the application, choosing a sign-in approach, copying a Client ID/Secret/Application ID, adding a redirect URI), tell the user exactly what to click and ask them to report the value back — one value at a time, confirming each before asking for the next. If the guide presents more than one configuration or sign-in approach, use the `AskUserQuestion` tool to let the user choose rather than guessing which one this project wants.

If the fetch fails (offline, docs unreachable), tell the user and give them the URL above to open manually. Do not improvise integration steps from memory.

## Project-specific notes

- Detect the package manager from lockfiles: `pnpm-lock.yaml` → `pnpm add`, `yarn.lock` → `yarn add`, `bun.lockb` → `bun add`, else `npm install`.
- Generate any session-signing secret the guide asks for with `openssl rand -base64 32`.
- Components registered by `@thunderid/nuxt` use the same bare names as `@thunderid/vue`/`@thunderid/react` (e.g. `SignedIn`, `SignInButton`, `SignIn`, `UserProfile`) and are auto-imported — no manual imports needed. `ThunderIDRoot` is the one exception, since it's the app-wrapping root component with no bare-name equivalent.

## Troubleshooting

- **Certificate error** — Visit `https://localhost:8090` in your browser and accept the warning once.
- **`invalid_client`** — Double-check the Client ID/Client Secret in `.env` against the application's General tab.
