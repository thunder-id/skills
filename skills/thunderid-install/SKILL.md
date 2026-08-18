---
name: thunderid-install
description: Install and start the ThunderID server. Use when asked to "set up ThunderID", "install ThunderID", "run ThunderID", or "start ThunderID server".
license: Apache-2.0
allowed-tools: Bash(npm:*), Bash(npx:*), Bash(lsof:*)
metadata:
  author: thunderid
  version: 1.0.0
---

# ThunderID Server Setup

```bash
npx thunderid
```

Downloads the latest ThunderID release for the current OS/arch, runs first-time setup, and starts the server — no manual steps or Docker required.

- Server starts at **https://localhost:8090**
- Console at **https://localhost:8090/console** — default credentials: `admin` / `admin`
- During setup, note the `[INFO] Sample App ID: <id>` line — you will need this Client ID when integrating an app

## Troubleshooting

**`Port 8090 is already in use`** — Another process is bound to 8090. Find and stop it with `lsof -i :8090`, or stop whatever previous ThunderID/other server instance is already running, then re-run `npx thunderid`.

**Certificate warnings** — ThunderID uses a self-signed HTTPS certificate on `localhost:8090`. Visit `https://localhost:8090` in your browser and accept the warning before running your app.

## What's Next

Choose the integration skill for your framework:

| Framework | Skill |
|-----------|-------|
| Next.js | `/thunderid-integrate-nextjs` |
| Nuxt | `/thunderid-integrate-nuxt` |
| React | `/thunderid-integrate-react` |
| Vue | `/thunderid-integrate-vue` |
| Vanilla JavaScript | `/thunderid-integrate-browser` |
| Express | `/thunderid-integrate-express` |
