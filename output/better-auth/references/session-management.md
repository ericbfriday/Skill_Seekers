# Better Auth Session Management

## Table of Contents

1. [Session Table](#session-table)
2. [Session Expiration](#session-expiration)
3. [Session Freshness](#session-freshness)
4. [Session Management Functions](#session-management-functions)
5. [Session Caching](#session-caching)
6. [Stateless Sessions](#stateless-sessions)
7. [Customizing Session Response](#customizing-session-response)

---

## Session Table

Sessions are stored with the following fields:

| Field | Type | Description |
|-------|------|-------------|
| id | string | Unique identifier |
| token | string | Session token (used as cookie) |
| userId | string | User ID reference |
| expiresAt | Date | Session expiration |
| ipAddress | string (optional) | Client IP |
| userAgent | string (optional) | Client user agent |

---

## Session Expiration

Default: 7 days, refreshed every 1 day.

```typescript
export const auth = betterAuth({
    session: {
        expiresIn: 60 * 60 * 24 * 7, // 7 days (in seconds)
        updateAge: 60 * 60 * 24, // Refresh every 1 day
    },
});
```

### Disable Session Refresh

```typescript
export const auth = betterAuth({
    session: {
        disableSessionRefresh: true, // Session never refreshes
    },
});
```

---

## Session Freshness

Some endpoints require a "fresh" session (recently created). Default `freshAge`: 1 day.

```typescript
export const auth = betterAuth({
    session: {
        freshAge: 60 * 5, // 5 minutes - session is fresh if created within 5 min
    },
});
```

### Disable Freshness Check

```typescript
export const auth = betterAuth({
    session: {
        freshAge: 0, // Disable freshness check
    },
});
```

---

## Session Management Functions

### Get Session (Client)

```typescript
const { data: session, error } = await authClient.getSession();
```

### Use Session Hook (Reactive)

```typescript
// React
const { data: session, isPending, error, refetch } = authClient.useSession();

// Vue
const session = authClient.useSession();

// Svelte
const session = authClient.useSession();
$: sessionData = $session.data;

// Solid
const session = authClient.useSession();

// Vanilla
authClient.useSession.subscribe((value) => {
    console.log(value);
});
```

### List Sessions

```typescript
const sessions = await authClient.listSessions();
```

### Revoke Session

```typescript
// Revoke specific session
await authClient.revokeSession({
    token: "session-token",
});
```

### Revoke Other Sessions

```typescript
// Keep current session, revoke all others
await authClient.revokeOtherSessions();
```

### Revoke All Sessions

```typescript
await authClient.revokeSessions();
```

### Revoke Sessions on Password Change

```typescript
await authClient.changePassword({
    newPassword: "newPassword",
    currentPassword: "currentPassword",
    revokeOtherSessions: true,
});
```

---

## Session Caching

### Cookie Cache

Avoid database calls on every `useSession`/`getSession` by caching in a signed cookie.

```typescript
export const auth = betterAuth({
    session: {
        cookieCache: {
            enabled: true,
            maxAge: 5 * 60, // 5 minutes cache duration
        },
    },
});
```

> **Note**: Revoked sessions may remain active on other devices until cache expires.

### Cookie Cache Strategies

| Strategy | Size | Security | Readable | Use Case |
|----------|------|----------|----------|----------|
| `compact` | Smallest | Good (signed) | Yes | Performance-critical |
| `jwt` | Medium | Good (signed) | Yes | JWT compatibility |
| `jwe` | Largest | Best (encrypted) | No | Maximum security |

```typescript
export const auth = betterAuth({
    session: {
        cookieCache: {
            enabled: true,
            maxAge: 5 * 60,
            strategy: "compact", // or "jwt" or "jwe"
        },
    },
});
```

### Bypass Cookie Cache

Force database fetch:

```typescript
// Client
const session = await authClient.getSession({
    query: { disableCookieCache: true },
});

// Server
await auth.api.getSession({
    query: { disableCookieCache: true },
    headers: req.headers,
});
```

---

## Stateless Sessions

Run Better Auth without a database using signed/encrypted cookies.

### Basic Stateless Setup

```typescript
import { betterAuth } from "better-auth";

export const auth = betterAuth({
    // No database configuration = automatic stateless mode
    socialProviders: {
        google: {
            clientId: process.env.GOOGLE_CLIENT_ID,
            clientSecret: process.env.GOOGLE_CLIENT_SECRET,
        },
    },
});
```

### Manual Stateless Configuration

```typescript
export const auth = betterAuth({
    session: {
        cookieCache: {
            enabled: true,
            maxAge: 7 * 24 * 60 * 60, // 7 days
            strategy: "jwe", // or "jwt", "compact"
            refreshCache: true, // Enable stateless refresh
        },
    },
    account: {
        storeStateStrategy: "cookie",
        storeAccountCookie: true,
    },
});
```

### refreshCache Options

- `false` (default): No automatic refresh
- `true`: Refresh at 80% of maxAge (20% remaining)
- `{ updateAge: number }`: Custom refresh timing

```typescript
session: {
    cookieCache: {
        enabled: true,
        maxAge: 300, // 5 minutes
        refreshCache: {
            updateAge: 60, // Refresh when 60 seconds remain
        },
    },
},
```

### Versioning Stateless Sessions

Invalidate all sessions by changing version:

```typescript
session: {
    cookieCache: {
        version: "2", // Change to invalidate all existing sessions
    },
},
```

### Stateless with Redis

Combine stateless cookies with Redis for revocation support:

```typescript
export const auth = betterAuth({
    secondaryStorage: {
        get: async (key) => await redis.get(key),
        set: async (key, value, ttl) => await redis.set(key, value, "EX", ttl),
        delete: async (key) => await redis.del(key),
    },
    session: {
        cookieCache: {
            maxAge: 5 * 60, // 5 minutes (short-lived)
            refreshCache: false, // Use Redis for refresh
        },
    },
});
```

---

## Customizing Session Response

Add custom data to `getSession`/`useSession` responses using the `customSession` plugin.

### Server Configuration

```typescript
import { customSession } from "better-auth/plugins";

export const auth = betterAuth({
    plugins: [
        customSession(async ({ user, session }) => {
            const roles = await findUserRoles(session.session.userId);
            return {
                roles,
                user: {
                    ...user,
                    newField: "value",
                },
                session,
            };
        }),
    ],
});
```

### Client Type Inference

```typescript
import { customSessionClient } from "better-auth/client/plugins";
import type { auth } from "@/lib/auth";

const authClient = createAuthClient({
    plugins: [customSessionClient<typeof auth>()],
});

const { data } = authClient.useSession();
// data.roles
// data.user.newField
```

### With Plugin Field Inference

Pass auth options to get full type inference:

```typescript
import { betterAuth, BetterAuthOptions } from "better-auth";

const options = {
    // ... config
    plugins: [/* ... */],
} satisfies BetterAuthOptions;

export const auth = betterAuth({
    ...options,
    plugins: [
        ...(options.plugins ?? []),
        customSession(
            async ({ user, session }, ctx) => {
                // user and session now infer plugin fields
                return { user, session };
            },
            options // pass options for inference
        ),
    ],
});
```

### Mutate listDeviceSessions

```typescript
customSession(
    async ({ user, session }, ctx) => {
        return { user, session };
    },
    {},
    { shouldMutateListDeviceSessionsEndpoint: true }
);
```

### Caveats

1. Plugin-added fields aren't automatically inferred (use options workaround)
2. Separate client/server projects can't import auth instance for type inference
3. Session caching doesn't include custom fields - callback runs on every fetch
