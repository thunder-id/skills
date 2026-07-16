---
name: thunderid-integrate-vue
description: Add ThunderID authentication to a Vue application using the official @thunderid/vue SDK. Use when asked to "integrate ThunderID into Vue", "add auth to my Vue app", or "connect ThunderID with Vue".
license: Apache-2.0
allowed-tools: Bash(npm:*), Bash(npx:*), Bash(pnpm:*), Bash(yarn:*), Bash(bun:*), Read, Write, Edit
metadata:
  author: thunderid
  version: 0.2.0
---

# ThunderID — Vue Integration

Assumes ThunderID is running at `https://localhost:8090`. If not, run `/thunderid-install` first.

## Step 1 — Create an Application

Open the Console at `https://localhost:8090/console`, navigate to **Applications**, and click **Add Application**:

1. Under **Technology**, select **Vue**.
2. Enter a name (e.g. `My Vue App`) and create an application. The rest of the settings can stay at their defaults.
3. Copy the **Client ID** from the **General** tab. You'll need it when configuring the SDK.
4. Under **General**, add `http://localhost:5173` to the list of **Authorized Redirect URIs** (adjust the port if your dev server uses a different one).

## Step 2 — Install

Detect the package manager from lockfiles: `pnpm-lock.yaml` → `pnpm add`, `yarn.lock` → `yarn add`, `bun.lockb` → `bun add`, else `npm install`.

```bash
npm install @thunderid/vue
```

## Step 3 — Set Environment Variables

Create `.env` (Vite exposes vars prefixed `VITE_` to client code):

```env
VITE_THUNDERID_CLIENT_ID=<your-client-id>
VITE_THUNDERID_BASE_URL=https://localhost:8090
```

## Step 4 — Register the Plugin

Edit `src/main.js`:

```js
import { createApp } from 'vue'
import { ThunderIDPlugin } from '@thunderid/vue'
import App from './App.vue'
import './style.css'

const app = createApp(App)
app.use(ThunderIDPlugin)
app.mount('#app')
```

## Step 5 — Add ThunderIDProvider

Edit `src/App.vue`:

```vue
<script setup>
import {
  SignedIn, SignedOut,
  SignInButton, UserDropdown,
} from '@thunderid/vue'
</script>

<template>
  <ThunderIDProvider
    :client-id="import.meta.env.VITE_THUNDERID_CLIENT_ID"
    :base-url="import.meta.env.VITE_THUNDERID_BASE_URL"
  >
    <SignedIn>
      <UserDropdown />
    </SignedIn>
    <SignedOut>
      <SignInButton>Sign In</SignInButton>
    </SignedOut>
  </ThunderIDProvider>
</template>
```

### Configuration Parameters

| Parameter | Description |
|-----------|-------------|
| `client-id` | The Client ID from your ThunderID application |
| `base-url` | Your ThunderID instance URL (e.g., `https://localhost:8090`) |

## Step 6 — Add a User Profile

To show a custom profile UI, use the `User` render-prop component on any view:

```vue
<script setup>
import { SignedIn, User } from '@thunderid/vue'
</script>

<template>
  <SignedIn>
    <User>
      <template #default="{ user }">
        <p>Welcome, {{ user.name || user.username }}!</p>
      </template>
    </User>
  </SignedIn>
</template>
```

## Step 7 — Run and Verify

```bash
npm run dev
```

You'll need a user to sign in with — if you haven't created one yet, open the Console, navigate to **Users**, and add a test user with an email and password.

Click **Sign In** — you should be redirected to `https://localhost:8090` and returned after login with the user dropdown displayed.

## Troubleshooting

**Certificate error** — Visit `https://localhost:8090` in your browser and accept the warning once.

**Composables fail outside `setup()`** — `useThunderID()` and `useUser()` use Vue's `inject` and must be called inside a component's `setup()`.

**`redirect_uri_mismatch`** — The dev server's origin must exactly match an entry in **Authorized Redirect URIs** on the application.
