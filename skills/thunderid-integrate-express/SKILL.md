---
name: thunderid-integrate-express
description: Add ThunderID authentication to an Express application using the official @thunderid/express SDK. Use when asked to "integrate ThunderID into Express", "add auth to my Express app", or "connect ThunderID with Express".
license: Apache-2.0
allowed-tools: WebFetch, Bash(npm:*), Bash(npx:*), Bash(pnpm:*), Bash(yarn:*), Bash(bun:*), Read, Write, Edit
metadata:
  author: thunderid
  version: 0.0.3
---

# ThunderID — Express Integration

Assumes ThunderID is running at `https://localhost:8090`. If not, run `/thunderid-install` first.

Fetch the canonical, always-current integration guide before writing any code:

WebFetch `https://thunderid.dev/docs/v1.0.x/getting-started/connect-your-application/express.md`

Follow that guide end to end — creating the Console application, installing `@thunderid/express`, adding the auth middleware and routes, protecting routes, and verifying the app runs — adapting file names, paths, and code style to this project's actual structure rather than copying the doc's file layout verbatim.

You do not have Console access or a browser tool. Whenever a step needs a Console UI action (creating the application, copying the Client ID/Secret, adding a redirect URI), tell the user exactly what to click and ask them to report the value back — one value at a time, confirming each before asking for the next.

If the fetch fails (offline, docs unreachable), tell the user and give them the URL above to open manually. Do not improvise integration steps from memory.

## Project-specific notes

- Detect the package manager from lockfiles: `pnpm-lock.yaml` → `pnpm add`, `yarn.lock` → `yarn add`, `bun.lockb` → `bun add`, else `npm install`.
- If Express is a resource server (an API called by another client, not a browser session), skip cookie-based session protection and validate the `Authorization: Bearer <token>` header directly against ThunderID's OIDC userinfo endpoint instead of the guide's cookie-session flow.

## Troubleshooting

- **Certificate error** — Visit `https://localhost:8090` in your browser and accept the warning once.
- **`invalid_client`** — Double-check the Client ID and Client Secret in `.env`.
- **Redirect loop after sign-in** — The configured post-sign-in URL must exactly match an entry in **Authorized Redirect URIs** on the application.
