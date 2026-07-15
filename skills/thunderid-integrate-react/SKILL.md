---
name: thunderid-integrate-react
description: Add ThunderID authentication to a React application using the official @thunderid/react SDK. Use when asked to "integrate ThunderID into React", "add auth to my React app", or "connect ThunderID with React".
license: Apache-2.0
allowed-tools: Bash(npm:*), Bash(npx:*), Bash(pnpm:*), Bash(yarn:*), Bash(bun:*), Read, Write, Edit
metadata:
  author: thunderid
  version: 0.2.0
---

# ThunderID — React Integration

Assumes ThunderID is running at `https://localhost:8090`. If not, run `/thunderid-install` first.

## Step 1 — Create an Application

Open the Console at `https://localhost:8090/console`, navigate to **Applications**, and click **Add Application**:

1. Under **Technology**, select **React**.
2. Enter a name (e.g. `My React App`) and create an application. The rest of the settings can stay at their defaults.
3. Copy the **Client ID** from the **General** tab. You'll need it when configuring the SDK.
4. Under **General**, add `http://localhost:5173` to the list of **Authorized Redirect URIs** (adjust the port if your dev server uses a different one).

## Step 2 — Install

Detect the package manager from lockfiles: `pnpm-lock.yaml` → `pnpm add`, `yarn.lock` → `yarn add`, `bun.lockb` → `bun add`, else `npm install`.

```bash
npm install @thunderid/react
```

## Step 3 — Set Environment Variables

Create `.env` (Vite exposes vars prefixed `VITE_` to client code):

```env
VITE_THUNDERID_CLIENT_ID=<your-client-id>
VITE_THUNDERID_BASE_URL=https://localhost:8090
```

## Step 4 — Wrap with Provider

Edit `src/main.jsx` (or `src/main.tsx`):

```jsx
import { StrictMode } from 'react'
import { createRoot } from 'react-dom/client'
import { ThunderIDProvider } from '@thunderid/react'
import App from './App.jsx'
import './index.css'

createRoot(document.getElementById('root')).render(
  <StrictMode>
    <ThunderIDProvider
      clientId={import.meta.env.VITE_THUNDERID_CLIENT_ID}
      baseUrl={import.meta.env.VITE_THUNDERID_BASE_URL}
    >
      <App />
    </ThunderIDProvider>
  </StrictMode>
)
```

### Configuration Parameters

| Parameter | Description |
|-----------|-------------|
| `clientId` | The Client ID from your ThunderID application |
| `baseUrl` | Your ThunderID instance URL (e.g., `https://localhost:8090`) |

## Step 5 — Add Auth UI

Use the prebuilt control components to show different content to signed-in and signed-out users:

- `<SignedIn>` / `<SignedOut>`: render children only in that auth state.
- `<SignInButton>`: unstyled, links to the ThunderID-hosted sign-in page.
- `<UserDropdown />`: shows the signed-in user's avatar; opens a dropdown with account management options.

```jsx
import {
  SignedIn, SignedOut,
  SignInButton, UserDropdown, Loading,
} from '@thunderid/react'

function App() {
  return (
    <>
      <Loading>
        <div>Loading authentication...</div>
      </Loading>
      <SignedOut>
        <SignInButton>Sign In</SignInButton>
      </SignedOut>
      <SignedIn>
        <UserDropdown />
      </SignedIn>
    </>
  )
}
```

To build a custom profile UI instead of `<UserDropdown />`, use the `<User>` render-prop component:

```jsx
import { User } from '@thunderid/react'

<User>
  {(user) => user && (
    <div>
      <h2>Welcome, {user.name}!</h2>
      <p>{user.email}</p>
    </div>
  )}
</User>
```

## Step 6 — Protect Routes (Optional)

If the app uses `react-router`, install `@thunderid/react-router` and wrap protected routes with `<ProtectedRoute>`:

```bash
npm install @thunderid/react-router react-router
```

```jsx
import { createBrowserRouter, RouterProvider } from 'react-router'
import { ProtectedRoute } from '@thunderid/react-router'
import Dashboard from './pages/Dashboard'

const router = createBrowserRouter([
  { path: '/', element: <App /> },
  {
    path: '/dashboard',
    element: <ProtectedRoute redirectTo="/"><Dashboard /></ProtectedRoute>,
  },
])
```

## Step 7 — Run and Verify

```bash
npm run dev
```

You'll need a user to sign in with — if you haven't created one yet, open the Console, navigate to **Users**, and add a test user with an email and password.

Click **Sign In** — you should be redirected to `https://localhost:8090` and returned after login with the user dropdown displayed.

## Troubleshooting

**Certificate error** — Visit `https://localhost:8090` in your browser and accept the warning once.

**React 19 Strict Mode double-render** — Expected in development; the SDK handles it internally.

**`redirect_uri_mismatch`** — The dev server's origin must exactly match an entry in **Authorized Redirect URIs** on the application.
