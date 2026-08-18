---
name: thunderid-integrate-nodejs
description: Add ThunderID authentication to a Node.js server application using the official @thunderid/node SDK. Use when asked to "integrate ThunderID into Node.js", "add auth to my Node server", or "connect ThunderID with Node".
license: Apache-2.0
allowed-tools: WebFetch, Bash(npm:*), Bash(npx:*), Bash(pnpm:*), Bash(yarn:*), Bash(bun:*), Read, Write, Edit
metadata:
  author: thunderid
  version: 0.0.3
---

# ThunderID — Node.js Integration

Assumes ThunderID is running at `https://localhost:8090`. If not, run `/thunderid-install` first.

Fetch the canonical, always-current integration guide before writing any code:

WebFetch `https://thunderid.dev/docs/v1.0.x/getting-started/connect-your-application/node.md`

Follow that guide end to end — creating the Console application, installing `@thunderid/node`, wiring up the client and auth routes, protecting routes, and verifying the app runs — adapting file names, paths, and code style to this project's actual structure rather than copying the doc's file layout verbatim.

You do not have Console access or a browser tool. Whenever a step needs a Console UI action (creating the application, copying the Client ID/Secret, adding a redirect URI), tell the user exactly what to click and ask them to report the value back — one value at a time, confirming each before asking for the next.

If the fetch fails (offline, docs unreachable), tell the user and give them the URL above to open manually. Do not improvise integration steps from memory.

## Project-specific notes

- Detect the package manager from lockfiles: `pnpm-lock.yaml` → `pnpm add`, `yarn.lock` → `yarn add`, `bun.lockb` → `bun add`, else `npm install`.
- If this server is an API/resource server (called by another client, not driving a browser session), the guide's cookie-session flow may not apply — prefer validating `Authorization: Bearer <token>` against ThunderID's OIDC userinfo endpoint if that's what the project needs. Ask the user which fits if it's unclear from the codebase.

## Troubleshooting

- **Certificate error** — Visit `https://localhost:8090` in your browser and accept the warning once, or configure the Node process to trust ThunderID's local certificate per the guide.
- **`invalid_client`** — Double-check the Client ID and Client Secret against the application's General tab.
