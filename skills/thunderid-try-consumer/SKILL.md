---
name: thunderid-try-consumer
description: Launch the Wayfinder B2C consumer login demo. Use when asked to "try ThunderID", "run a consumer demo", "see a sample app", "show social login", or "test email/passkey login".
license: Apache-2.0
allowed-tools: Bash(npx:*)
metadata:
  author: thunderid
  version: 0.1.0
---

# ThunderID — Consumer Login Demo (Wayfinder)

Assumes ThunderID is running at `https://localhost:8090`. If not, run `/install` first.

## What Is Wayfinder?

Wayfinder is a B2C consumer app that demonstrates:

- Email and password login
- Social provider login (Google, GitHub, etc.)
- Passkeys
- Multi-factor authentication (MFA)
- Sign-up, profile management, and account recovery

## Launch the Demo

```bash
npx thunderid try wayfinder
```

ThunderID downloads the Wayfinder sample app, seeds it with demo data, and starts the frontend and backend services.

The app opens at **http://localhost:5173**.

## What's Next

Once the demo is running, you can add ThunderID auth to your own app:

| Framework | Skill |
|-----------|-------|
| Next.js | `/integrate-nextjs` |
| React | `/integrate-react` |
| Vue | `/integrate-vue` |
| Nuxt | `/integrate-nuxt` |
