![ThunderID Skills](./assets/images/repo-banner.png)

## Install

### via Claude Code

```bash
/plugin marketplace add thunder-id/skills
/plugin install thunderid-skills@thunderid-skills
```

### via Codex

```bash
codex plugin marketplace add thunder-id/skills
```

After adding the marketplace, restart Codex, open `/plugins`, select
**ThunderID Skills**, install and enable `thunder-id-skills`, then start a new thread.

### via Open Agent Skills CLI

```bash
npx skills add thunder-id/skills
```

## Skills

| Skill | Purpose | When to Use |
| --- | --- | --- |
| `thunderid-install` | Install and start the ThunderID server | New projects, initial setup |
| `thunderid-try-consumer` | Launch the Wayfinder B2C consumer login demo | Try ThunderID, see a sample app |
| `thunderid-try-agentid` | Launch the AgentID AI agent authentication demo | Try AgentID, demo AI agent auth |
| `thunderid-integrate-react` | `@thunderid/react` | React apps |
| `thunderid-integrate-nextjs` | `@thunderid/nextjs` | Next.js apps |
| `thunderid-integrate-express` | `@thunderid/express` | Express apps |
| `thunderid-integrate-vue` | `@thunderid/vue` | Vue apps |
| `thunderid-integrate-nuxt` | `@thunderid/nuxt` | Nuxt apps |
| `thunderid-integrate-nodejs` | `@thunderid/node` | Node.js servers |
| `thunderid-integrate-browser` | `@thunderid/browser` | Vanilla JavaScript apps |
| `thunderid-integrate-ios` | ThunderID iOS SDK | iOS (SwiftUI) apps |
| `thunderid-integrate-android` | ThunderID Android SDK | Android (Compose) apps |
| `thunderid-integrate-flutter` | `thunderid_flutter` | Flutter apps |

## Usage

### Ask Your Agent

| You Say | Skill Used |
| --- | --- |
| "Set up ThunderID on my machine" | `thunderid-install` |
| "Try ThunderID" | `thunderid-try-consumer` |
| "Demo AI agent auth" | `thunderid-try-agentid` |
| "Integrate ThunderID into my React app" | `thunderid-integrate-react` |
| "Add ThunderID to my Next.js app" | `thunderid-integrate-nextjs` |
| "Add ThunderID to my Express app" | `thunderid-integrate-express` |
| "Add ThunderID to my Vue app" | `thunderid-integrate-vue` |
| "Add ThunderID to my Nuxt app" | `thunderid-integrate-nuxt` |
| "Add ThunderID to my Node server" | `thunderid-integrate-nodejs` |
| "Integrate ThunderID into vanilla JS" | `thunderid-integrate-browser` |
| "Add ThunderID to my iOS app" | `thunderid-integrate-ios` |
| "Add ThunderID to my Android app" | `thunderid-integrate-android` |
| "Add ThunderID to my Flutter app" | `thunderid-integrate-flutter` |

## License

Licenses this source under the Apache License, Version 2.0 ([LICENSE](LICENSE)), You may not use this file except in compliance with the License.
