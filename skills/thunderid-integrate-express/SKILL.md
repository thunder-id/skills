---
name: thunderid-integrate-express
description: Add ThunderID authentication to an Express application using the official @thunderid/express SDK. Use when asked to "integrate ThunderID into Express", "add auth to my Express app", or "connect ThunderID with Express".
license: Apache-2.0
allowed-tools: Bash(npm:*), Bash(npx:*), Bash(pnpm:*), Bash(yarn:*), Bash(bun:*), Read, Write, Edit
metadata:
  author: thunderid
  version: 0.1.0
---

# ThunderID — Express Integration

Assumes ThunderID is running at `https://localhost:8090`. If not, run `/thunderid-install` first.

## Step 1 — Create an Application

Open the Console at `https://localhost:8090/console`, navigate to **Applications**, and click **Add Application**:

1. From the **Choose a type** page, select **Express**.
2. Enter a name (e.g. `My Express App`) and create an application. The rest of the settings can stay at their defaults.
3. Copy the **Client ID** and **Client Secret** from the window that pops up (also available on the **General** tab's Quick Copy section).
4. Under **General**, add `http://localhost:3000/login` to the list of **Authorized Redirect URIs**.

## Step 2 — Install

Detect the package manager from lockfiles: `pnpm-lock.yaml` → `pnpm add`, `yarn.lock` → `yarn add`, `bun.lockb` → `bun add`, else `npm install`.

```bash
npm install @thunderid/express express cookie-parser
```

## Step 3 — Set Environment Variables

Create `.env`:

```env
THUNDERID_BASE_URL=https://localhost:8090
THUNDERID_CLIENT_ID=<your-client-id>
THUNDERID_CLIENT_SECRET=<your-client-secret>
# DANGER: Disables ALL TLS verification. Only for local development with self-signed certs. NEVER use in production.
NODE_TLS_REJECT_UNAUTHORIZED=0
```

## Step 4 — Add Authentication Middleware and Routes

Create `index.mjs` (or `index.js` with `"type": "module"` in `package.json`) with ThunderID middleware and auth routes:

```js
import express from 'express';
import cookieParser from 'cookie-parser';
import { thunderID, handleSignIn, handleSignOut, protect } from '@thunderid/express';

const app = express();
const port = 3000;

app.use(cookieParser());
app.use(express.json());

app.use(
  thunderID({
    baseUrl: process.env.THUNDERID_BASE_URL,
    clientId: process.env.THUNDERID_CLIENT_ID,
    clientSecret: process.env.THUNDERID_CLIENT_SECRET,
    // Doubles as the OAuth2 redirect_uri sent to ThunderID — must match the
    // Authorized Redirect URI registered on the application (Step 1.4).
    afterSignInUrl: 'http://localhost:3000/login',
    afterSignOutUrl: 'http://localhost:3000/logout',
  }),
);

app.get('/', (_req, res) => {
  res.send('<a href="/protected">Go to protected page</a>');
});

app.get('/login', handleSignIn());
app.get('/logout', handleSignOut());

app.get(
  '/protected',
  protect((res) => res.redirect('/login')),
  (_req, res) => {
    res.send('You are signed in and can access this protected route.');
  },
);

app.get('/me', protect(), async (req, res) => {
  const user = await req.thunderIDAuth.getUserFromRequest(req);
  res.json(user);
});

app.listen(port, () => {
  console.log(`Server running on http://localhost:${port}`);
});
```

- `handleSignIn()` / `handleSignOut()`: mount the redirect-to-ThunderID and callback-handling routes.
- `protect(onUnauthenticated?)`: guards a route; without an argument it responds `401` instead of redirecting.
- `req.thunderIDAuth`: the underlying client, attached by the `thunderID()` middleware — use it to read the signed-in user or session.

## Step 5 — Run and Verify

```bash
node index.mjs
```

You'll need a user to sign in with — if you haven't created one yet, open the Console, navigate to **Users**, and add a test user with an email and password.

Open `http://localhost:3000/protected` — you should be redirected to ThunderID sign-in, then returned to the protected route after authenticating. Open `http://localhost:3000/me` to see the signed-in user's profile.

## Step 6 — Protect an API with Bearer Tokens (Optional)

If Express is a resource server (an API called by another client, not a browser session), skip cookie-based `protect()` and validate the `Authorization: Bearer <token>` header directly against ThunderID's OIDC userinfo endpoint instead:

```js
async function requireBearer(req, res, next) {
  const [scheme, token] = (req.headers.authorization || '').split(' ');
  if (scheme !== 'Bearer' || !token) {
    res.set('WWW-Authenticate', 'Bearer');
    return res.status(401).json({ error: 'unauthorized' });
  }

  const response = await fetch(`${process.env.THUNDERID_BASE_URL}/oauth2/userinfo`, {
    headers: { Authorization: `Bearer ${token}` },
  });
  if (!response.ok) {
    res.set('WWW-Authenticate', 'Bearer error="invalid_token"');
    return res.status(401).json({ error: 'invalid_token' });
  }

  req.thunderIDUserInfo = await response.json();
  next();
}

app.get('/api/me', requireBearer, (req, res) => {
  res.json(req.thunderIDUserInfo);
});
```

Get a token to test with by signing in via `/login` (Step 4) and reading `req.thunderIDAuth.getAccessToken(sessionId)`, or via any OAuth 2.0 client credentials / authorization code flow against your application.

## Troubleshooting

**Certificate error** — Visit `https://localhost:8090` in your browser and accept the warning once.

**`invalid_client`** — Double-check the Client ID and Client Secret in `.env`.

**Redirect loop on `/login`** — `afterSignInUrl` must exactly match an entry in **Authorized Redirect URIs** on the application.
