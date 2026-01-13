# Better Auth Basic Usage

## Table of Contents

1. [Email & Password Authentication](#email--password-authentication)
2. [Social Sign-On](#social-sign-on)
3. [Signout](#signout)
4. [Session Management](#session-management)
5. [Using Plugins](#using-plugins)

---

## Email & Password Authentication

Enable email and password authentication in your auth config:

```typescript
// auth.ts
import { betterAuth } from "better-auth";

export const auth = betterAuth({
    emailAndPassword: {
        enabled: true,
    },
});
```

### Sign Up

```typescript
import { authClient } from "@/lib/auth-client";

const { data, error } = await authClient.signUp.email({
    email: "user@example.com",
    password: "securePassword123", // min 8 characters by default
    name: "John Doe",
    image: "https://example.com/avatar.jpg", // optional
    callbackURL: "/dashboard", // optional redirect after email verification
}, {
    onRequest: (ctx) => {
        // Show loading state
    },
    onSuccess: (ctx) => {
        // Redirect to dashboard or sign in page
    },
    onError: (ctx) => {
        // Display error message
        alert(ctx.error.message);
    },
});
```

#### Disable Auto Sign-In After Sign Up

```typescript
export const auth = betterAuth({
    emailAndPassword: {
        enabled: true,
        autoSignIn: false, // defaults to true
    },
});
```

### Sign In

```typescript
const { data, error } = await authClient.signIn.email({
    email: "user@example.com",
    password: "securePassword123",
    callbackURL: "/dashboard", // optional redirect URL
    rememberMe: true, // default: true - remember session after browser close
}, {
    // Optional callbacks
    onRequest: (ctx) => { /* show loading */ },
    onSuccess: (ctx) => { /* handle success */ },
    onError: (ctx) => { /* handle error */ },
});
```

> **Important**: Always invoke client methods from the client side. Don't call them from the server.

### Server-Side Authentication

Use `auth.api` methods for server-side authentication:

```typescript
import { auth } from "./auth";

const response = await auth.api.signInEmail({
    body: {
        email: "user@example.com",
        password: "password123",
    },
    asResponse: true, // returns a Response object instead of data
});
```

> **Note**: For frameworks that can't return Response objects directly, you'll need to manually parse and set cookies. For Next.js, use the [nextCookies plugin](nextjs-integration.md#server-action-cookies).

---

## Social Sign-On

Better Auth supports 35+ social providers including Google, GitHub, Apple, Discord, and more.

### Configure Providers

```typescript
// auth.ts
import { betterAuth } from "better-auth";

export const auth = betterAuth({
    socialProviders: {
        github: {
            clientId: process.env.GITHUB_CLIENT_ID!,
            clientSecret: process.env.GITHUB_CLIENT_SECRET!,
        },
        google: {
            clientId: process.env.GOOGLE_CLIENT_ID!,
            clientSecret: process.env.GOOGLE_CLIENT_SECRET!,
        },
        discord: {
            clientId: process.env.DISCORD_CLIENT_ID!,
            clientSecret: process.env.DISCORD_CLIENT_SECRET!,
        },
    },
});
```

### Sign In with Social Providers

```typescript
import { authClient } from "@/lib/auth-client";

await authClient.signIn.social({
    provider: "github", // or "google", "discord", "apple", etc.
    callbackURL: "/dashboard", // redirect after authentication
    errorCallbackURL: "/error", // redirect if error occurs
    newUserCallbackURL: "/welcome", // redirect if user is newly registered
    disableRedirect: false, // set true to disable automatic redirect
});
```

### Using ID Token or Access Token

You can authenticate using tokens from the social provider instead of redirecting:

```typescript
await authClient.signIn.social({
    provider: "google",
    idToken: "google-id-token", // or accessToken
});
```

See [social providers documentation](social-providers.md) for more details.

---

## Signout

```typescript
await authClient.signOut();
```

### With Redirect

```typescript
await authClient.signOut({
    fetchOptions: {
        onSuccess: () => {
            router.push("/login");
        },
    },
});
```

---

## Session Management

### Client Side

#### useSession Hook (Reactive)

The `useSession` hook provides reactive session data that updates automatically when the session changes.

**React:**
```tsx
import { authClient } from "@/lib/auth-client";

export function User() {
    const {
        data: session,
        isPending, // loading state
        error,
        refetch,
    } = authClient.useSession();

    if (isPending) return <div>Loading...</div>;
    if (!session) return <div>Not logged in</div>;

    return <div>Welcome {session.user.name}</div>;
}
```

**Vue:**
```vue
<script setup lang="ts">
import { authClient } from "~/lib/auth-client";

const session = authClient.useSession();
</script>

<template>
    <div>
        <pre>{{ session.data }}</pre>
        <button v-if="session.data" @click="authClient.signOut()">
            Sign out
        </button>
    </div>
</template>
```

**Svelte:**
```svelte
<script lang="ts">
import { authClient } from "$lib/auth-client";

const session = authClient.useSession();
</script>

<p>{$session.data?.user.email}</p>
```

**Solid:**
```tsx
import { authClient } from "~/lib/auth-client";

export default function Home() {
    const session = authClient.useSession();
    return <pre>{JSON.stringify(session(), null, 2)}</pre>;
}
```

**Vanilla:**
```typescript
import { authClient } from "~/lib/auth-client";

authClient.useSession.subscribe((value) => {
    // Handle session changes
});
```

#### getSession (Non-Reactive)

```typescript
import { authClient } from "@/lib/auth-client";

const { data: session, error } = await authClient.getSession();
```

Works with data-fetching libraries like TanStack Query.

### Server Side

Retrieve session using `auth.api.getSession()` with request headers:

**Next.js:**
```typescript
import { auth } from "./auth";
import { headers } from "next/headers";

const session = await auth.api.getSession({
    headers: await headers(),
});
```

**Remix:**
```typescript
import { auth } from "lib/auth";

export async function loader({ request }: LoaderFunctionArgs) {
    const session = await auth.api.getSession({
        headers: request.headers,
    });
    return json({ session });
}
```

**Astro:**
```astro
---
import { auth } from "./auth";

const session = await auth.api.getSession({
    headers: Astro.request.headers,
});
---
```

**SvelteKit:**
```typescript
import { auth } from "./auth";

export async function load({ request }) {
    const session = await auth.api.getSession({
        headers: request.headers,
    });
    return { props: { session } };
}
```

**Hono:**
```typescript
import { auth } from "./auth";

app.get("/path", async (c) => {
    const session = await auth.api.getSession({
        headers: c.req.raw.headers,
    });
});
```

**Nuxt:**
```typescript
import { auth } from "~/utils/auth";

export default defineEventHandler((event) => {
    const session = await auth.api.getSession({
        headers: event.headers,
    });
});
```

**TanStack:**
```typescript
import { auth } from "./auth";
import { createAPIFileRoute } from "@tanstack/start/api";

export const APIRoute = createAPIFileRoute("/api/$")({
    GET: async ({ request }) => {
        const session = await auth.api.getSession({
            headers: request.headers,
        });
    },
});
```

---

## Using Plugins

Plugins extend Better Auth with advanced functionality. Example: Two-Factor Authentication.

### 1. Server Configuration

```typescript
// auth.ts
import { betterAuth } from "better-auth";
import { twoFactor } from "better-auth/plugins";

export const auth = betterAuth({
    // ... other options
    plugins: [
        twoFactor(),
    ],
});
```

### 2. Migrate Database

After adding plugins, add required tables:

```bash
# Generate schema
npx @better-auth/cli generate

# Or migrate directly
npx @better-auth/cli migrate
```

### 3. Client Configuration

```typescript
// auth-client.ts
import { createAuthClient } from "better-auth/client";
import { twoFactorClient } from "better-auth/client/plugins";

const authClient = createAuthClient({
    plugins: [
        twoFactorClient({
            twoFactorPage: "/two-factor", // redirect page for 2FA verification
        }),
    ],
});
```

### 4. Use Plugin Methods

```typescript
import { authClient } from "./auth-client";

// Enable two factor
const enableTwoFactor = async () => {
    const data = await authClient.twoFactor.enable({
        password: "user-password", // required
    });
    // Returns totpURI and backupCodes
};

// Disable two factor
const disableTwoFactor = async () => {
    const data = await authClient.twoFactor.disable({
        password: "user-password",
    });
};

// Sign in with 2FA
const signInWith2Factor = async () => {
    const data = await authClient.signIn.email({
        email: "user@example.com",
        password: "password",
    });
    // If 2FA enabled, redirects to two factor page
};

// Verify TOTP code
const verifyTOTP = async () => {
    const data = await authClient.twoFactor.verifyTOTP({
        code: "123456",
        trustDevice: true, // device won't need 2FA for 30 days
    });
};
```

See [two-factor.md](two-factor.md) for complete 2FA documentation.

---

## Next Steps

- [Session Management](session-management.md) - Advanced session configuration
- [Database Configuration](database.md) - Database adapters and schema
- [Social Providers](social-providers.md) - OAuth provider setup
- [Plugins](plugins.md) - Available plugins and creating custom ones
