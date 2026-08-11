---
name: thunderid-integrate-browser
description: Add ThunderID authentication to a framework-free (vanilla) JavaScript application using the official @thunderid/browser SDK. Use when asked to "integrate ThunderID into vanilla JS", "add auth to my browser app", or "connect ThunderID with plain JavaScript".
license: Apache-2.0
allowed-tools: Bash(npm:*), Bash(npx:*), Bash(pnpm:*), Bash(yarn:*), Bash(bun:*), Read, Write, Edit
metadata:
  author: thunderid
  version: 0.1.0
---

# ThunderID — Vanilla JavaScript Integration

Assumes ThunderID is running at `https://localhost:8090`. If not, run `/thunderid-install` first.

## Step 1 — Create an Application

Open the Console at `https://localhost:8090/console`, navigate to **Applications**, and click **Add Application**:

1. From the **Choose a type** page, select **JavaScript**.
2. Enter a name (e.g. `My JavaScript App`) and create an application. The rest of the settings can stay at their defaults.
3. Copy the **Client ID** from the **General** tab. You'll need it when configuring the SDK.
4. Under **General**, add `http://localhost:5173` to the list of **Authorized Redirect URIs** (adjust the port if your dev server uses a different one).

## Step 2 — Install

Detect the package manager from lockfiles: `pnpm-lock.yaml` → `pnpm add`, `yarn.lock` → `yarn add`, `bun.lockb` → `bun add`, else `npm install`.

```bash
npm install @thunderid/browser
```

## Step 3 — Set Environment Variables

If using Vite, create `.env`:

```env
VITE_THUNDERID_CLIENT_ID=<your-client-id>
VITE_THUNDERID_BASE_URL=https://localhost:8090
```

## Step 4 — Initialize the SDK

`ThunderIDBrowserClient` is the main entry point. Create `src/auth.js`:

```js
import { ThunderIDBrowserClient } from '@thunderid/browser'

const auth = new ThunderIDBrowserClient()

await auth.initialize({
  clientId: import.meta.env.VITE_THUNDERID_CLIENT_ID,
  baseUrl: import.meta.env.VITE_THUNDERID_BASE_URL,
  afterSignInUrl: window.location.origin,
  afterSignOutUrl: window.location.origin,
})

export default auth
```

### Configuration Parameters

| Parameter | Description |
|-----------|-------------|
| `clientId` | The Client ID from your ThunderID application |
| `baseUrl` | Your ThunderID instance URL (e.g., `https://localhost:8090`) |
| `afterSignInUrl` | URL to redirect to after sign-in (defaults to current origin) |
| `afterSignOutUrl` | URL to redirect to after sign-out (defaults to current origin) |

## Step 5 — Add Sign-In and Sign-Out

Every page load, `signIn({ callOnlyOnRedirect: true })` completes the OAuth callback if one is in progress (it's a no-op otherwise). Replace `src/main.js`:

```js
import './style.css'
import auth from './auth.js'

async function renderApp() {
  await auth.signIn({ callOnlyOnRedirect: true })

  const isSignedIn = await auth.isSignedIn()

  if (isSignedIn) {
    const user = await auth.getUser()

    document.querySelector('#app').innerHTML = `
      <div>
        <h1>ThunderID Auth Demo</h1>
        <div class="card">
          <h2>Welcome, ${user.displayName || user.username}!</h2>
          <p><strong>Email:</strong> ${user.email || 'N/A'}</p>
          <button id="sign-out-btn" type="button">Sign Out</button>
        </div>
      </div>
    `

    document.querySelector('#sign-out-btn')
      .addEventListener('click', () => auth.signOut())
  } else {
    document.querySelector('#app').innerHTML = `
      <div>
        <h1>ThunderID Auth Demo</h1>
        <div class="card">
          <p>You are not signed in.</p>
          <button id="sign-in-btn" type="button">Sign In</button>
        </div>
      </div>
    `

    document.querySelector('#sign-in-btn')
      .addEventListener('click', () => auth.signIn())
  }
}

renderApp()
```

## Step 6 — Run and Verify

```bash
npm run dev
```

You'll need a user to sign in with — if you haven't created one yet, open the Console, navigate to **Users**, and add a test user with an email and password.

Click **Sign In** — you should be redirected to `https://localhost:8090` and returned after login with the user profile displayed.

## Troubleshooting

**Certificate error** — Visit `https://localhost:8090` in your browser and accept the warning once.

**`redirect_uri_mismatch`** — The dev server's origin must exactly match an entry in **Authorized Redirect URIs** on the application.

**Blank page / stuck on redirect** — Make sure `signIn({ callOnlyOnRedirect: true })` runs before checking `isSignedIn()`; it's what completes the OAuth code exchange after the redirect back from ThunderID.
