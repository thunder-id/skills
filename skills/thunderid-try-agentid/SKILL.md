---
name: thunderid-try-agentid
description: Launch the AgentID demo for AI agent authentication. Use when asked to "try AgentID", "demo AI agent auth", "show agent identity", "test ThunderID with an AI agent", or "run the AI concierge demo".
license: Apache-2.0
allowed-tools: Bash(npx:*)
metadata:
  author: thunderid
  version: 0.1.0
---

# ThunderID — AgentID Demo

Assumes ThunderID is running at `https://localhost:8090`. If not, run `/install` first.

## What Is AgentID?

AgentID demonstrates secure access for AI agents and automated workflows acting on behalf of users — an AI concierge that authenticates as a real ThunderID user and calls backend APIs with delegated credentials.

## Before You Start

You need an LLM API key for the AI concierge service. Supported providers:

| Provider | Env var |
|----------|---------|
| Anthropic (Claude) | `LLM_API_KEY` |
| Gemini | `LLM_API_KEY` |

Ask the developer which provider they want to use and collect their API key.

## Launch the Demo

```bash
npx thunderid try wayfinder
```

The CLI will prompt for:

1. **LLM provider** — select Anthropic (Claude) or Gemini
2. **API key** — your provider API key (input is masked)

ThunderID then downloads the Wayfinder sample with AI features enabled, seeds it, and starts all services including the AI agent backend.

The app opens at **http://localhost:5173**.

## What's Next

Once the demo is running, you can add ThunderID auth to your own app:

| Framework | Skill |
|-----------|-------|
| Next.js | `/integrate-nextjs` |
| React | `/integrate-react` |
| Vue | `/integrate-vue` |
| Nuxt | `/integrate-nuxt` |
