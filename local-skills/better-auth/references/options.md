# Better Auth Configuration Options

Complete reference for all Better Auth configuration options.

## Table of Contents

1. [Core Options](#core-options)
2. [Authentication](#authentication)
3. [Session](#session)
4. [Database](#database)
5. [Security](#security)
6. [Advanced](#advanced)

---

## Core Options

### appName

Application name, used for email templates and TOTP issuer.

```typescript
export const auth = betterAuth({
    appName: "My App",
});
```

### baseURL

Base URL where auth server is hosted.

```typescript
export const auth = betterAuth({
    baseURL: "https://example.com",
});
```

Falls back to `BETTER_AUTH_URL` environment variable, then infers from request.

### basePath

Path where auth routes are mounted.

```typescript
export const auth = betterAuth({
    basePath: "/api/auth", // default
});
```

### secret

Secret for encryption, signing, and hashing. Minimum 32 characters.

```typescript
export const auth = betterAuth({
    secret: "your-secret-key-at-least-32-characters",
});
```

Falls back to `BETTER_AUTH_SECRET` or `AUTH_SECRET` environment variables.

Generate a secret:
```bash
openssl rand -base64 32
```

### trustedOrigins

Allowed origins for CORS and redirects.

```typescript
export const auth = betterAuth({
    // Static list
    trustedOrigins: ["http://localhost:3000", "https://example.com"],

    // Dynamic function
    trustedOrigins: async (request) => {
        if (!request) return ["https://default.com"];
        return ["https://dynamic.com"];
    },

    // Wildcard patterns
    trustedOrigins: [
        "https://*.example.com", // all subdomains
        "exp://192.168.*.*:*/**", // Expo development
    ],
});
```

### plugins

Array of Better Auth plugins.

```typescript
import { twoFactor, admin } from "better-auth/plugins";

export const auth = betterAuth({
    plugins: [twoFactor(), admin()],
});
```

### disabledPaths

Disable specific auth endpoints.

```typescript
export const auth = betterAuth({
    disabledPaths: ["/sign-up/email", "/sign-in/email"],
});
```

---

## Authentication

### emailAndPassword

Email/password authentication configuration.

```typescript
export const auth = betterAuth({
    emailAndPassword: {
        enabled: true,
        disableSignUp: false,
        requireEmailVerification: true,
        minPasswordLength: 8, // default
        maxPasswordLength: 128, // default
        autoSignIn: true, // auto sign-in after signup

        // Custom password hashing
        password: {
            hash: async (password) => {
                return await bcrypt.hash(password, 10);
            },
            verify: async ({ hash, password }) => {
                return await bcrypt.compare(password, hash);
            },
        },

        // Password reset
        sendResetPassword: async ({ user, url, token }) => {
            await sendEmail(user.email, { body: `Reset: ${url}` });
        },
        resetPasswordTokenExpiresIn: 3600, // 1 hour
    },
});
```

### emailVerification

Email verification settings.

```typescript
export const auth = betterAuth({
    emailVerification: {
        sendVerificationEmail: async ({ user, url, token }) => {
            await sendEmail(user.email, { body: `Verify: ${url}` });
        },
        sendOnSignUp: true,
        sendOnSignIn: false,
        autoSignInAfterVerification: true,
        expiresIn: 3600, // 1 hour
    },
});
```

### socialProviders

OAuth social provider configuration.

```typescript
export const auth = betterAuth({
    socialProviders: {
        google: {
            clientId: process.env.GOOGLE_CLIENT_ID!,
            clientSecret: process.env.GOOGLE_CLIENT_SECRET!,
            redirectURI: "https://example.com/api/auth/callback/google",
            scope: ["profile", "email"], // additional scopes
            prompt: "select_account",
            mapProfileToUser: (profile) => ({
                firstName: profile.given_name,
            }),
            disableSignUp: false,
            overrideUserInfoOnSignIn: false,
        },
    },
});
```

---

## Session

```typescript
export const auth = betterAuth({
    session: {
        modelName: "session", // table name
        
        // Field mapping
        fields: {
            userId: "user_id",
        },

        expiresIn: 60 * 60 * 24 * 7, // 7 days
        updateAge: 60 * 60 * 24, // refresh every 1 day
        freshAge: 60 * 60 * 24, // fresh session duration
        disableSessionRefresh: false,

        // Additional fields
        additionalFields: {
            deviceId: { type: "string" },
        },

        // Cookie caching
        cookieCache: {
            enabled: true,
            maxAge: 300, // 5 minutes
            strategy: "compact", // "compact", "jwt", "jwe"
            refreshCache: true,
            version: "1",
        },

        // Secondary storage options
        storeSessionInDatabase: true,
        preserveSessionInDatabase: false,
    },
});
```

---

## Database

### database

Database connection configuration.

```typescript
import { Pool } from "pg";
import { drizzleAdapter } from "better-auth/adapters/drizzle";

export const auth = betterAuth({
    // Direct connection
    database: new Pool({ connectionString: process.env.DATABASE_URL }),

    // ORM adapter
    database: drizzleAdapter(db, { provider: "pg" }),
});
```

### secondaryStorage

Key-value storage for sessions and rate limiting.

```typescript
export const auth = betterAuth({
    secondaryStorage: {
        get: async (key) => await redis.get(key),
        set: async (key, value, ttl) => {
            if (ttl) await redis.set(key, value, "EX", ttl);
            else await redis.set(key, value);
        },
        delete: async (key) => await redis.del(key),
    },
});
```

---

## Security

### rateLimit

Rate limiting configuration.

```typescript
export const auth = betterAuth({
    rateLimit: {
        enabled: true, // default: true in production
        window: 10, // seconds
        max: 100, // requests per window

        // Custom rules
        customRules: {
            "/sign-in/email": {
                window: 60,
                max: 10,
            },
        },

        // Storage: "memory", "database", "secondary-storage"
        storage: "memory",
        modelName: "rateLimit",
    },
});
```

### account

Account configuration and linking.

```typescript
export const auth = betterAuth({
    account: {
        modelName: "account",
        fields: { userId: "user_id" },

        encryptOAuthTokens: false,
        updateAccountOnSignIn: false,
        storeAccountCookie: false,

        accountLinking: {
            enabled: true,
            trustedProviders: ["google", "github"],
            allowDifferentEmails: false,
            allowUnlinkingAll: false,
        },
    },
});
```

---

## Advanced

### user

User table configuration.

```typescript
export const auth = betterAuth({
    user: {
        modelName: "user",
        fields: {
            email: "email_address",
            name: "full_name",
        },

        // Additional fields
        additionalFields: {
            role: {
                type: ["user", "admin"],
                defaultValue: "user",
                input: false,
            },
        },

        // Email change
        changeEmail: {
            enabled: true,
            sendChangeEmailConfirmation: async ({ user, newEmail, url }) => {
                await sendEmail(user.email, { body: `Confirm: ${url}` });
            },
            updateEmailWithoutVerification: false,
        },

        // Account deletion
        deleteUser: {
            enabled: true,
            sendDeleteAccountVerification: async ({ user, url }) => {
                await sendEmail(user.email, { body: `Delete: ${url}` });
            },
            beforeDelete: async (user) => { /* cleanup */ },
            afterDelete: async (user) => { /* cleanup */ },
        },
    },
});
```

### verification

Verification table configuration.

```typescript
export const auth = betterAuth({
    verification: {
        modelName: "verification",
        fields: { userId: "user_id" },
        disableCleanup: false,
    },
});
```

### advanced

Advanced configuration options.

```typescript
export const auth = betterAuth({
    advanced: {
        // IP tracking
        ipAddress: {
            ipAddressHeaders: ["x-client-ip", "x-forwarded-for"],
            disableIpTracking: false,
        },

        // Security
        useSecureCookies: true,
        disableCSRFCheck: false,
        disableOriginCheck: false,

        // Cross-subdomain cookies
        crossSubDomainCookies: {
            enabled: true,
            domain: "example.com",
            additionalCookies: ["custom_cookie"],
        },

        // Cookie customization
        cookiePrefix: "myapp",
        cookies: {
            session_token: {
                name: "custom_session",
                attributes: { httpOnly: true, secure: true },
            },
        },
        defaultCookieAttributes: {
            httpOnly: true,
            secure: true,
        },

        // ID generation
        database: {
            generateId: false, // let DB generate
            // generateId: "serial", // auto-increment
            // generateId: "uuid", // UUID
            // generateId: (opts) => customId(), // custom function
            defaultFindManyLimit: 100,
        },
    },
});
```

### logger

Logging configuration.

```typescript
export const auth = betterAuth({
    logger: {
        disabled: false,
        disableColors: false,
        level: "info", // "info", "warn", "error", "debug"

        // Custom logger
        log: (level, message, ...args) => {
            myLogger.log({ level, message, metadata: args });
        },
    },
});
```

### databaseHooks

Database lifecycle hooks.

```typescript
export const auth = betterAuth({
    databaseHooks: {
        user: {
            create: {
                before: async (user, ctx) => {
                    return { data: { ...user, role: "user" } };
                },
                after: async (user) => {
                    await createStripeCustomer(user);
                },
            },
            update: {
                before: async (data, ctx) => ({ data }),
                after: async (user) => { /* sync */ },
            },
            delete: {
                before: async (user, ctx) => true, // return false to abort
                after: async (user) => { /* cleanup */ },
            },
        },
        session: { /* same structure */ },
        account: { /* same structure */ },
    },
});
```

### hooks

Request lifecycle hooks.

```typescript
import { createAuthMiddleware } from "better-auth/api";

export const auth = betterAuth({
    hooks: {
        before: createAuthMiddleware(async (ctx) => {
            console.log("Before:", ctx.path);
        }),
        after: createAuthMiddleware(async (ctx) => {
            console.log("After:", ctx.context.returned);
        }),
    },
});
```

### onAPIError

API error handling.

```typescript
export const auth = betterAuth({
    onAPIError: {
        throw: false,
        errorURL: "/api/auth/error",

        onError: (error, ctx) => {
            console.error("Auth error:", error);
        },

        // Customize error page
        customizeDefaultErrorPage: {
            colors: {
                background: "#ffffff",
                foreground: "#000000",
                primary: "#0070f3",
            },
            disableTitleBorder: false,
            disableCornerDecorations: false,
        },
    },
});
```

### telemetry

Anonymous telemetry (disabled by default).

```typescript
export const auth = betterAuth({
    telemetry: {
        enabled: false,
    },
});
```

### experimental

Experimental features.

```typescript
export const auth = betterAuth({
    experimental: {
        joins: true, // Enable database joins
    },
});
```

---

## Environment Variables

Better Auth checks these environment variables:

| Variable | Purpose |
|----------|---------|
| `BETTER_AUTH_SECRET` | Secret key |
| `AUTH_SECRET` | Fallback secret key |
| `BETTER_AUTH_URL` | Base URL |

```env
BETTER_AUTH_SECRET=your-secret-key-32-chars-minimum
BETTER_AUTH_URL=http://localhost:3000
```
