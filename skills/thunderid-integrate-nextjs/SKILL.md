---
name: thunderid-integrate-nextjs
description: Add ThunderID authentication to a Next.js application using the official @thunderid/nextjs SDK. Use when asked to "integrate ThunderID into Next.js", "add auth to my Next.js app", or "connect ThunderID with Next.js".
license: Apache-2.0
allowed-tools: Bash(npm:*), Bash(npx:*), Bash(pnpm:*), Bash(yarn:*), Bash(bun:*), Read, Write, Edit
metadata:
  author: thunderid
  version: 0.2.0
---

# ThunderID — Next.js Integration

Assumes ThunderID is running at `https://localhost:8090`. If not, run `/thunderid-install` first.

## Step 1 — Choose a Sign-In Experience

Use the `AskUserQuestion` tool (single-select) to ask the user how they'd like to handle sign-in/sign-up — do not ask this as plain text. Use exactly these two options:

- **Redirect to ThunderID sign-in/sign-up pages** — `<SignInButton>` sends the browser to the ThunderID-hosted pages (a full navigation away from the app). Only requires the **Client ID**.
- **Embedded sign-in/sign-up components in the app** — `<SignIn>`/`<SignUp>` render inline, in the app's own pages. Requires the **Application ID** and **Client Secret** (the Client ID is not needed).

Use the answer to decide which credentials to collect in Step 2 and whether to do Step 8.

## Step 2 — Create an Application

You (the agent) do not have access to the Console — do not attempt to create the application yourself. Ask the user to create it and report back the values:

Tell the user to open the Console at `https://localhost:8090/console`, navigate to **Applications**, click **Add Application**, and:

1. From the **Choose a type** page, select **Next.js**.
2. Enter a name (e.g. `My Next.js App`) and create an application. The rest of the settings can stay at their defaults.
3. From the window that pops up:
   - **Redirect**: copy the **Client ID**.
   - **Embedded**: copy the **Client Secret**, and the **Application ID** from the **General** tab's Quick Copy section (this is different from the Client ID). The Client ID is not needed.
4. Under **General**, add `http://localhost:3000` to the list of **Authorized Redirect URIs**.

Then collect the values needed for the flow chosen in Step 1 one at a time — ask for a single value, wait for the reply, confirm you got it, then ask for the next. Do not bundle multiple values into one request.

- **Redirect**: ask for the **Client ID**.
- **Embedded**: ask for the **Client Secret** first, then ask for the **Application ID** as a separate follow-up question.

Do not proceed to Step 3 until every required value for the chosen flow has been provided.

## Step 3 — Install

Detect the package manager from lockfiles: `pnpm-lock.yaml` → `pnpm add`, `yarn.lock` → `yarn add`, `bun.lockb` → `bun add`, else `npm install`.

```bash
npm install @thunderid/nextjs
```

## Step 4 — Set Environment Variables

Create `.env.local`. For **Redirect**:

```env
NEXT_PUBLIC_THUNDERID_BASE_URL=https://localhost:8090
NEXT_PUBLIC_THUNDERID_CLIENT_ID=<your-client-id>
THUNDERID_SECRET=<a-random-secret-for-session-signing>
# Remove in production:
NODE_TLS_REJECT_UNAUTHORIZED=0
```

For **Embedded** (no Client ID needed):

```env
NEXT_PUBLIC_THUNDERID_BASE_URL=https://localhost:8090
THUNDERID_CLIENT_SECRET=<your-client-secret>
NEXT_PUBLIC_THUNDERID_APPLICATION_ID=<your-application-id>
THUNDERID_SECRET=<a-random-secret-for-session-signing>
# Remove in production:
NODE_TLS_REJECT_UNAUTHORIZED=0
```

Generate `THUNDERID_SECRET` with `openssl rand -base64 32` (at least 32 characters).

## Step 5 — Add ThunderIDProvider to Layout

Edit `app/layout.tsx` — `ThunderIDProvider` handles the OAuth callback automatically; no manual callback route needed:

```tsx
import { ThunderIDProvider } from '@thunderid/nextjs/server'

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en">
      <body>
        <ThunderIDProvider>
          {children}
        </ThunderIDProvider>
      </body>
    </html>
  )
}
```

## Step 6 — Add the ThunderID Proxy

Create `proxy.ts` at the project root to protect specific routes:

```ts
import {
  thunderIDProxy,
  createRouteMatcher,
} from '@thunderid/nextjs/server'

const isProtectedRoute = createRouteMatcher(['/dashboard(.*)'])

export default thunderIDProxy(
  async (thunderid, request) => {
    if (isProtectedRoute(request))
      await thunderid.protectRoute()
  }
)

export const config = {
  matcher: [
    '/((?!_next/static|_next/image|favicon.ico).*)',
  ],
}
```

## Step 7 — Add Auth UI

```tsx
import {
  SignedIn,
  SignedOut,
  SignInButton,
  UserDropdown,
} from "@thunderid/nextjs"

export default function Home() {
  return (
    <section>
      <SignedIn>
        <UserDropdown />
      </SignedIn>
      <SignedOut>
        <SignInButton>Sign In</SignInButton>
      </SignedOut>
    </section>
  )
}
```

By default, `<SignInButton>` redirects the browser to the ThunderID-hosted sign-in page (a full navigation away from your app). If **Redirect** was chosen in Step 1, this works out of the box — skip to Step 9.

## Step 8 — Embedded Sign-In / Sign-Up Page

Only needed if **Embedded** was chosen in Step 1. Point `SignInButton` at local routes instead of the hosted page:

1. Add to `.env.local` (Application ID and Client Secret should already be set from Step 4):

   ```env
   NEXT_PUBLIC_THUNDERID_SIGN_IN_URL=/signin
   NEXT_PUBLIC_THUNDERID_SIGN_UP_URL=/signup
   ```

   Once `NEXT_PUBLIC_THUNDERID_SIGN_IN_URL` is set, `<SignInButton>` navigates to that local route instead of redirecting to the hosted page.

2. Create `app/signin/page.tsx`:

   ```tsx
   'use client'
   import { useRouter } from 'next/navigation'
   import { SignedIn, SignedOut, SignIn } from '@thunderid/nextjs'

   export default function SignInPage() {
     const router = useRouter()
     return (
       <>
         <SignedIn>{/* already signed in — redirect or show a link home */}</SignedIn>
         <SignedOut>
           <SignIn onSuccess={() => router.push('/')} />
         </SignedOut>
       </>
     )
   }
   ```

3. Create `app/signup/page.tsx`:

   ```tsx
   'use client'
   import { SignedIn, SignedOut, SignUp } from '@thunderid/nextjs'

   export default function SignUpPage() {
     return (
       <SignedOut>
         <SignUp afterSignUpUrl="/" />
       </SignedOut>
     )
   }
   ```

`<SignIn>` and `<SignUp>` render the full sign-in/sign-up flow (including MFA, passkeys, or social login steps configured in the Flow Designer) inline, in your app's own layout.

## Step 9 — Run and Verify

```bash
npm run dev
```

You'll need a user to sign in with — if you haven't created one yet, open the Console, navigate to **Users**, and add a test user with an email and password.

Click **Sign In** — depending on whether Redirect or Embedded was chosen in Step 1, you'll either be redirected to `https://localhost:8090` or taken to your app's own `/signin` page, and returned with the user dropdown displayed.

## Troubleshooting

**Certificate error** — Visit `https://localhost:8090` in your browser and accept the warning once.

**`invalid_client`** — Double-check the Client ID (Redirect) or Client Secret (Embedded) in `.env.local`.

**Embedded `<SignIn>`/`<SignUp>` renders blank or errors** — Verify `NEXT_PUBLIC_THUNDERID_APPLICATION_ID` is set and matches the Application ID (not the Client ID) shown on the application's General tab.
