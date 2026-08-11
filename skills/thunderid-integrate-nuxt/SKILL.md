---
name: thunderid-integrate-nuxt
description: Add ThunderID authentication to a Nuxt application using the official @thunderid/nuxt module. Use when asked to "integrate ThunderID into Nuxt", "add auth to my Nuxt app", or "connect ThunderID with Nuxt".
license: Apache-2.0
allowed-tools: Bash(npm:*), Bash(npx:*), Bash(pnpm:*), Bash(yarn:*), Bash(bun:*), Read, Write, Edit
metadata:
  author: thunderid
  version: 0.2.0
---

# ThunderID — Nuxt Integration

Assumes ThunderID is running at `https://localhost:8090`. If not, run `/thunderid-install` first.

All components registered by `@thunderid/nuxt` use a `ThunderID` prefix (e.g. `ThunderIDSignedIn`, `ThunderIDSignInButton`) so they don't collide with your own component names. They are auto-imported — no manual imports needed.

## Step 1 — Choose a Sign-In Experience

Use the `AskUserQuestion` tool (single-select) to ask the user how they'd like to handle sign-in/sign-up — do not ask this as plain text. Use exactly these two options:

- **Redirect to ThunderID sign-in/sign-up pages** — `<ThunderIDSignInButton>` sends the browser to the ThunderID-hosted pages (a full navigation away from the app). Only requires the **Client ID**.
- **Embedded sign-in/sign-up components in the app** — `<ThunderIDSignIn>`/`<ThunderIDSignUp>` render inline, in the app's own pages. Requires the **Application ID** and **Client Secret** (the Client ID is not needed).

Use the answer to decide which credentials to collect in Step 2 and whether to do Step 9.

## Step 2 — Create an Application

You (the agent) do not have access to the Console — do not attempt to create the application yourself. Ask the user to create it and report back the values:

Tell the user to open the Console at `https://localhost:8090/console`, navigate to **Applications**, click **Add Application**, and:

1. From the **Choose a type** page, select **Nuxt**.
2. Enter a name (e.g. `My Nuxt App`) and create an application. The rest of the settings can stay at their defaults.
3. From the window that pops up:
   - **Redirect**: copy the **Client ID**.
   - **Embedded**: copy the **Client Secret**, and the **Application ID** from the **General** tab's Quick Copy section (this is different from the Client ID). The Client ID is not needed.
4. Under **General**, add `http://localhost:3000/api/auth/callback` to the list of **Authorized Redirect URIs**.

Then collect the values needed for the flow chosen in Step 1 one at a time — ask for a single value, wait for the reply, confirm you got it, then ask for the next. Do not bundle multiple values into one request.

- **Redirect**: ask for the **Client ID**.
- **Embedded**: ask for the **Client Secret** first, then ask for the **Application ID** as a separate follow-up question.

Do not proceed to Step 3 until every required value for the chosen flow has been provided.

## Step 3 — Install

Detect the package manager from lockfiles: `pnpm-lock.yaml` → `pnpm add`, `yarn.lock` → `yarn add`, `bun.lockb` → `bun add`, else `npm install`.

```bash
npm install @thunderid/nuxt
```

## Step 4 — Register the Module

Edit `nuxt.config.ts`:

```ts
export default defineNuxtConfig({
  modules: ['@thunderid/nuxt'],
})
```

The module auto-registers `/api/auth/callback` (and the rest of the auth API) as Nitro server routes — no manual callback route needed. All configuration is read from environment variables.

## Step 5 — Set Environment Variables

Create `.env`. For **Redirect**:

```env
NUXT_PUBLIC_THUNDERID_BASE_URL=https://localhost:8090
NUXT_PUBLIC_THUNDERID_CLIENT_ID=<your-client-id>
THUNDERID_SESSION_SECRET=<random-string-at-least-32-chars>
```

For **Embedded** (no Client ID needed):

```env
NUXT_PUBLIC_THUNDERID_BASE_URL=https://localhost:8090
THUNDERID_CLIENT_SECRET=<your-client-secret>
NUXT_PUBLIC_THUNDERID_APPLICATION_ID=<your-application-id>
THUNDERID_SESSION_SECRET=<random-string-at-least-32-chars>
```

Generate `THUNDERID_SESSION_SECRET` with `openssl rand -base64 32`. `THUNDERID_CLIENT_SECRET` and `THUNDERID_SESSION_SECRET` have no `NUXT_PUBLIC_` prefix — Nuxt keeps them server-side only.

## Step 6 — Wrap App with ThunderIDRoot

Edit `app.vue`:

```vue
<template>
  <ThunderIDRoot>
    <NuxtPage />
  </ThunderIDRoot>
</template>
```

## Step 7 — Add Auth UI

Create `pages/index.vue`:

```vue
<template>
  <main>
    <ThunderIDSignedIn>
      <ThunderIDUserDropdown />
    </ThunderIDSignedIn>
    <ThunderIDSignedOut>
      <ThunderIDSignInButton>Sign In</ThunderIDSignInButton>
    </ThunderIDSignedOut>
  </main>
</template>
```

By default, `<ThunderIDSignInButton>` redirects the browser to the ThunderID-hosted sign-in page. If **Redirect** was chosen in Step 1, this works out of the box — skip Step 9.

## Step 8 — Protect a Page

Apply the pre-built named `'auth'` middleware — no imports needed:

```vue
<script setup>
definePageMeta({ middleware: 'auth' })
</script>
```

Unauthenticated users are redirected to `/api/auth/signin`. To require specific OIDC scopes instead, define a custom middleware with `defineThunderIDMiddleware({ requireScopes: [...], redirectTo: '/unauthorized' })`.

## Step 9 — Embedded Sign-In / Sign-Up Page

Only needed if **Embedded** was chosen in Step 1. Point `ThunderIDSignInButton` at local routes instead of the hosted page:

1. Add to `.env` (Application ID and Client Secret should already be set from Step 5):

   ```env
   NUXT_PUBLIC_THUNDERID_SIGN_IN_URL=/signin
   NUXT_PUBLIC_THUNDERID_SIGN_UP_URL=/signup
   ```

   Once `NUXT_PUBLIC_THUNDERID_SIGN_IN_URL` is set, `<ThunderIDSignInButton>` navigates to that local route instead of redirecting to the hosted page.

2. Create `pages/signin.vue`:

   ```vue
   <template>
     <ThunderIDSignedIn>
       <NuxtLink to="/">Already signed in — go home</NuxtLink>
     </ThunderIDSignedIn>
     <ThunderIDSignedOut>
       <ThunderIDSignIn />
     </ThunderIDSignedOut>
   </template>
   ```

3. Create `pages/signup.vue`:

   ```vue
   <template>
     <ThunderIDSignedOut>
       <ThunderIDSignUp after-sign-up-url="/" />
     </ThunderIDSignedOut>
   </template>
   ```

`<ThunderIDSignIn>` and `<ThunderIDSignUp>` render the full sign-in/sign-up flow (including MFA, passkeys, or social login steps configured in the Flow Designer) inline, in your app's own layout.

## Step 10 — Run and Verify

```bash
npm run dev
```

You'll need a user to sign in with — if you haven't created one yet, open the Console, navigate to **Users**, and add a test user with an email and password.

Click **Sign In** — depending on whether Redirect or Embedded was chosen in Step 1, you'll either be redirected to `https://localhost:8090` or taken to your app's own `/signin` page, and returned with the user dropdown displayed.

## Troubleshooting

**Certificate error** — Visit `https://localhost:8090` in your browser and accept the warning once.

**`invalid_client`** — Double-check the Client ID (Redirect) or Client Secret (Embedded) in `.env`.

**Component not found (e.g. `<SignedIn>` fails but `<ThunderIDSignedIn>` works)** — Nuxt components from this module are always prefixed with `ThunderID`; there are no unprefixed exports.

**Embedded `<ThunderIDSignIn>`/`<ThunderIDSignUp>` renders blank or errors** — Verify `NUXT_PUBLIC_THUNDERID_APPLICATION_ID` is set and matches the Application ID (not the Client ID) shown on the application's General tab.
