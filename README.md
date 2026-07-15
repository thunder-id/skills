![ThunderID Skills](./assets/images/repo-banner.png)

## Install

### via Open Agent Skills CLI

```bash
npx skills add thunder-id/skills
```

### via Codex

```bash
codex plugin marketplace add thunder-id/skills
```

After adding the marketplace, restart Codex, open `/plugins`, select
**ThunderID Skills**, install and enable `thunder-id-skills`, then start a new thread.

### via Claude Code

```bash
/plugin marketplace add thunder-id/skills
```

## Skills

| Skill | Purpose | When to Use |
| --- | --- | --- |
| `thunderid-install` | Install and start the ThunderID server | New projects, initial setup |
| `thunderid-try-consumer` | Launch the Wayfinder B2C consumer login demo | Try ThunderID, see a sample app |
| `thunderid-try-agentid` | Launch the AgentID AI agent authentication demo | Try AgentID, demo AI agent auth |
| `thunderid-integrate-nextjs` | `@thunderid/nextjs` | Next.js apps |
| `thunderid-integrate-react` | `@thunderid/react` | React apps |
| `thunderid-integrate-vue` | `@thunderid/vue` | Vue apps |
| `thunderid-integrate-nuxt` | `@thunderid/nuxt` | Nuxt apps |

## Usage

### Ask Your Agent

| You Say | Skill Used |
| --- | --- |
| "Set up ThunderID on my machine" | `thunderid-install` |
| "Try ThunderID" | `thunderid-try-consumer` |
| "Demo AI agent auth" | `thunderid-try-agentid` |
| "Add ThunderID to my Next.js app" | `thunderid-integrate-nextjs` |
| "Integrate ThunderID into my React app" | `thunderid-integrate-react` |
| "Add ThunderID to my Vue app" | `thunderid-integrate-vue` |
| "Add ThunderID to my Nuxt app" | `thunderid-integrate-nuxt` |

## License

Licenses this source under the Apache License, Version 2.0 ([LICENSE](LICENSE)), You may not use this file except in compliance with the License.

---------------------------------------------------------------------------
(c) Copyright 2026 WSO2 LLC.
