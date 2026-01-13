---
name: better-auth
description: Better Auth - The most comprehensive authentication framework for TypeScript. Use for implementing email/password auth, social OAuth providers (Google, GitHub, Discord, Apple, etc.), two-factor authentication (2FA/TOTP/OTP), passkeys, magic links, session management, database adapters (Prisma, Drizzle, MongoDB), and integrating with frameworks like Next.js, Nuxt, SvelteKit, Remix, Astro, Hono, Express. Includes 40+ plugins for organizations, API keys, SSO, OIDC, and more.
---

# Better Auth Skill

Better Auth is a framework-agnostic, universal authentication and authorization framework for TypeScript. It provides comprehensive features out of the box with a plugin ecosystem for advanced functionalities like 2FA, passkeys, multi-tenancy, multi-session support, SSO, and creating your own IDP.

## When to Use This Skill

This skill should be triggered when:
- Implementing authentication in TypeScript/JavaScript applications
- Setting up email/password, social OAuth, or passwordless authentication
- Configuring two-factor authentication (2FA, TOTP, OTP)
- Working with session management and cookies
- Integrating auth with Next.js, Nuxt, SvelteKit, Remix, Astro, Hono, Express
- Setting up database adapters (Prisma, Drizzle, PostgreSQL, MySQL, MongoDB)
- Implementing organization/team management
- Creating API key authentication
- Setting up SSO or OIDC providers

## AI Tooling

Better Auth provides native AI integration:

### LLMs.txt
Better Auth exposes an `LLMs.txt` at https://better-auth.com/llms.txt

### MCP Server
Better Auth provides a first-party MCP server:

```bash
# Claude Code
claude mcp add --transport http better-auth https://mcp.chonkie.ai/better-auth/better-auth-builder/mcp

# CLI Options
npx @better-auth/cli mcp --cursor      # Cursor
npx @better-auth/cli mcp --claude-code # Claude Code
npx @better-auth/cli mcp --open-code   # Open Code
npx @better-auth/cli mcp --manual      # Manual config
```

## Quick Reference

### Installation

```bash
npm install better-auth
# or
pnpm add better-auth
# or
yarn add better-auth
# or
bun add better-auth
```

### Environment Variables

```env
BETTER_AUTH_SECRET=your-secret-key-min-32-chars
BETTER_AUTH_URL=http://localhost:3000
```

### Basic Server Setup

```typescript
// auth.ts
import { betterAuth } from "better-auth";

export const auth = betterAuth({
    database: new Pool({ connectionString: process.env.DATABASE_URL }),
    emailAndPassword: {
        enabled: true,
    },
    socialProviders: {
        github: {
            clientId: process.env.GITHUB_CLIENT_ID!,
            clientSecret: process.env.GITHUB_CLIENT_SECRET!,
        },
        google: {
            clientId: process.env.GOOGLE_CLIENT_ID!,
            clientSecret: process.env.GOOGLE_CLIENT_SECRET!,
        },
    },
});
```

### Client Setup

```typescript
// auth-client.ts
import { createAuthClient } from "better-auth/react"; // or /vue, /svelte, /solid, /client

export const authClient = createAuthClient({
    baseURL: "http://localhost:3000", // optional if same domain
});

// Export specific methods
export const { signIn, signUp, useSession } = authClient;
```

### Database Migration

```bash
# Generate schema for your ORM
npx @better-auth/cli generate

# Or migrate directly (Kysely adapter only)
npx @better-auth/cli migrate
```

## Common Patterns

### Pattern 1: Next.js App Router Setup

**Route Handler:**
```typescript
// app/api/auth/[...all]/route.ts
import { auth } from "@/lib/auth";
import { toNextJsHandler } from "better-auth/next-js";

export const { POST, GET } = toNextJsHandler(auth);
```

**Server Component Session:**
```typescript
// app/dashboard/page.tsx
import { auth } from "@/lib/auth";
import { headers } from "next/headers";
import { redirect } from "next/navigation";

export default async function DashboardPage() {
    const session = await auth.api.getSession({
        headers: await headers()
    });

    if (!session) {
        redirect("/sign-in");
    }

    return <h1>Welcome {session.user.name}</h1>;
}
```

**Middleware (Cookie Check):**
```typescript
// middleware.ts
import { NextRequest, NextResponse } from "next/server";
import { getSessionCookie } from "better-auth/cookies";

export async function middleware(request: NextRequest) {
    const sessionCookie = getSessionCookie(request);

    if (!sessionCookie && request.nextUrl.pathname.startsWith("/dashboard")) {
        return NextResponse.redirect(new URL("/login", request.url));
    }

    return NextResponse.next();
}

export const config = {
    matcher: ["/dashboard/:path*"],
};
```

**Server Actions with Cookies:**
```typescript
// auth.ts - Add nextCookies plugin
import { betterAuth } from "better-auth";
import { nextCookies } from "better-auth/next-js";

export const auth = betterAuth({
    // ... config
    plugins: [nextCookies()], // must be last plugin
});
```

### Pattern 2: Email/Password Authentication

**Sign Up:**
```typescript
const { data, error } = await authClient.signUp.email({
    email: "user@example.com",
    password: "securePassword123",
    name: "John Doe",
    image: "https://example.com/avatar.jpg", // optional
    callbackURL: "/dashboard", // optional redirect after verification
}, {
    onRequest: (ctx) => { /* show loading */ },
    onSuccess: (ctx) => { /* redirect to dashboard */ },
    onError: (ctx) => { alert(ctx.error.message); },
});
```

**Sign In:**
```typescript
const { data, error } = await authClient.signIn.email({
    email: "user@example.com",
    password: "securePassword123",
    callbackURL: "/dashboard",
    rememberMe: true, // default: true
});
```

**Sign Out:**
```typescript
await authClient.signOut({
    fetchOptions: {
        onSuccess: () => {
            router.push("/login");
        },
    },
});
```

### Pattern 3: Social OAuth Authentication

**Client-side Sign In:**
```typescript
await authClient.signIn.social({
    provider: "github", // or "google", "discord", "apple", etc.
    callbackURL: "/dashboard",
    errorCallbackURL: "/error",
    newUserCallbackURL: "/welcome",
});
```

**Server Configuration:**
```typescript
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
        apple: {
            clientId: process.env.APPLE_CLIENT_ID!,
            clientSecret: process.env.APPLE_CLIENT_SECRET!,
        },
    },
});
```

### Pattern 4: Session Management

**Client-side useSession Hook:**
```typescript
// React
import { authClient } from "@/lib/auth-client";

export function UserProfile() {
    const { data: session, isPending, error, refetch } = authClient.useSession();

    if (isPending) return <div>Loading...</div>;
    if (!session) return <div>Not logged in</div>;

    return <div>Welcome {session.user.name}</div>;
}
```

**Server-side getSession:**
```typescript
// Next.js
import { auth } from "@/lib/auth";
import { headers } from "next/headers";

const session = await auth.api.getSession({
    headers: await headers(),
});
```

**Session Configuration:**
```typescript
export const auth = betterAuth({
    session: {
        expiresIn: 60 * 60 * 24 * 7, // 7 days
        updateAge: 60 * 60 * 24, // refresh every 1 day
        cookieCache: {
            enabled: true,
            maxAge: 5 * 60, // 5 minutes
        },
    },
});
```

### Pattern 5: Two-Factor Authentication (2FA)

**Server Setup:**
```typescript
import { betterAuth } from "better-auth";
import { twoFactor } from "better-auth/plugins";

export const auth = betterAuth({
    appName: "My App", // used as TOTP issuer
    plugins: [
        twoFactor({
            otpOptions: {
                async sendOTP({ user, otp }, ctx) {
                    // Send OTP via email/SMS
                    await sendEmail(user.email, `Your code: ${otp}`);
                },
            },
        }),
    ],
});
```

**Client Setup:**
```typescript
import { createAuthClient } from "better-auth/react";
import { twoFactorClient } from "better-auth/client/plugins";

const authClient = createAuthClient({
    plugins: [
        twoFactorClient({
            onTwoFactorRedirect() {
                window.location.href = "/2fa";
            },
        }),
    ],
});
```

**Enable 2FA:**
```typescript
const { data, error } = await authClient.twoFactor.enable({
    password: "userPassword",
});
// Returns totpURI and backupCodes
```

**Verify TOTP:**
```typescript
const { data, error } = await authClient.twoFactor.verifyTotp({
    code: "123456",
    trustDevice: true, // remember device for 30 days
});
```

**Handle 2FA on Sign In:**
```typescript
await authClient.signIn.email({
    email: "user@example.com",
    password: "password",
}, {
    onSuccess(context) {
        if (context.data.twoFactorRedirect) {
            // User needs to verify 2FA
            router.push("/2fa");
        }
    },
});
```

### Pattern 6: Database Adapters

**PostgreSQL (Direct):**
```typescript
import { betterAuth } from "better-auth";
import { Pool } from "pg";

export const auth = betterAuth({
    database: new Pool({
        connectionString: process.env.DATABASE_URL,
    }),
});
```

**Drizzle ORM:**
```typescript
import { betterAuth } from "better-auth";
import { drizzleAdapter } from "better-auth/adapters/drizzle";
import { db } from "@/db";

export const auth = betterAuth({
    database: drizzleAdapter(db, {
        provider: "pg", // or "mysql", "sqlite"
    }),
});
```

**Prisma:**
```typescript
import { betterAuth } from "better-auth";
import { prismaAdapter } from "better-auth/adapters/prisma";
import { PrismaClient } from "@prisma/client";

const prisma = new PrismaClient();

export const auth = betterAuth({
    database: prismaAdapter(prisma, {
        provider: "postgresql",
    }),
});
```

**MongoDB:**
```typescript
import { betterAuth } from "better-auth";
import { mongodbAdapter } from "better-auth/adapters/mongodb";
import { client } from "@/db";

export const auth = betterAuth({
    database: mongodbAdapter(client),
});
```

### Pattern 7: Email Verification

```typescript
export const auth = betterAuth({
    emailAndPassword: {
        enabled: true,
        requireEmailVerification: true,
    },
    emailVerification: {
        sendVerificationEmail: async ({ user, url, token }) => {
            await sendEmail(user.email, {
                subject: "Verify your email",
                body: `Click here to verify: ${url}`,
            });
        },
        sendOnSignUp: true,
        autoSignInAfterVerification: true,
        expiresIn: 3600, // 1 hour
    },
});
```

### Pattern 8: Password Reset

```typescript
export const auth = betterAuth({
    emailAndPassword: {
        enabled: true,
        sendResetPassword: async ({ user, url, token }) => {
            await sendEmail(user.email, {
                subject: "Reset your password",
                body: `Click here to reset: ${url}`,
            });
        },
        resetPasswordTokenExpiresIn: 3600, // 1 hour
    },
});
```

**Client-side:**
```typescript
// Request reset
await authClient.forgetPassword({
    email: "user@example.com",
    redirectTo: "/reset-password",
});

// Reset password
await authClient.resetPassword({
    newPassword: "newSecurePassword",
    token: "reset-token-from-url",
});
```

### Pattern 9: Extending User Schema

```typescript
export const auth = betterAuth({
    user: {
        additionalFields: {
            role: {
                type: ["user", "admin"],
                required: false,
                defaultValue: "user",
                input: false, // don't allow user to set
            },
            lang: {
                type: "string",
                required: false,
                defaultValue: "en",
            },
        },
    },
});

// Access on signup response
const res = await auth.api.signUpEmail({
    email: "test@example.com",
    password: "password",
    name: "John",
    lang: "fr",
});
console.log(res.user.role); // "user"
console.log(res.user.lang); // "fr"
```

### Pattern 10: Plugins System

**Adding Multiple Plugins:**
```typescript
import { betterAuth } from "better-auth";
import { 
    twoFactor, 
    admin, 
    organization,
    passkey,
    magicLink,
    username,
} from "better-auth/plugins";

export const auth = betterAuth({
    plugins: [
        twoFactor(),
        admin(),
        organization(),
        passkey(),
        magicLink({
            sendMagicLink: async ({ email, url }) => {
                await sendEmail(email, `Login: ${url}`);
            },
        }),
        username(),
    ],
});
```

**Client Plugins:**
```typescript
import { createAuthClient } from "better-auth/react";
import { 
    twoFactorClient,
    adminClient,
    organizationClient,
    passkeyClient,
    magicLinkClient,
    usernameClient,
} from "better-auth/client/plugins";

export const authClient = createAuthClient({
    plugins: [
        twoFactorClient(),
        adminClient(),
        organizationClient(),
        passkeyClient(),
        magicLinkClient(),
        usernameClient(),
    ],
});
```

## Core Database Schema

Better Auth requires these core tables:

### User Table
| Field | Type | Description |
|-------|------|-------------|
| id | string | Primary key |
| name | string | Display name |
| email | string | Email address |
| emailVerified | boolean | Verification status |
| image | string? | Avatar URL |
| createdAt | Date | Creation timestamp |
| updatedAt | Date | Update timestamp |

### Session Table
| Field | Type | Description |
|-------|------|-------------|
| id | string | Primary key |
| userId | string | Foreign key to user |
| token | string | Unique session token |
| expiresAt | Date | Expiration time |
| ipAddress | string? | Client IP |
| userAgent | string? | Client user agent |
| createdAt | Date | Creation timestamp |
| updatedAt | Date | Update timestamp |

### Account Table
| Field | Type | Description |
|-------|------|-------------|
| id | string | Primary key |
| userId | string | Foreign key to user |
| accountId | string | Provider's user ID |
| providerId | string | Provider identifier |
| accessToken | string? | OAuth access token |
| refreshToken | string? | OAuth refresh token |
| password | string? | Hashed password |
| createdAt | Date | Creation timestamp |
| updatedAt | Date | Update timestamp |

### Verification Table
| Field | Type | Description |
|-------|------|-------------|
| id | string | Primary key |
| identifier | string | Verification identifier |
| value | string | Verification value |
| expiresAt | Date | Expiration time |
| createdAt | Date | Creation timestamp |
| updatedAt | Date | Update timestamp |

## Framework Integrations

Better Auth supports all major frameworks:

| Framework | Handler | Import |
|-----------|---------|--------|
| Next.js App Router | `toNextJsHandler(auth)` | `better-auth/next-js` |
| Next.js Pages Router | `toNodeHandler(auth.handler)` | `better-auth/node` |
| Nuxt | `auth.handler(toWebRequest(event))` | Direct |
| SvelteKit | `svelteKitHandler({ event, resolve, auth })` | `better-auth/svelte-kit` |
| Remix | `auth.handler(request)` | Direct |
| Solid Start | `toSolidStartHandler(auth)` | `better-auth/solid-start` |
| Astro | `auth.handler(ctx.request)` | Direct |
| Hono | `auth.handler(c.req.raw)` | Direct |
| Express | `toNodeHandler(auth)` | `better-auth/node` |
| Elysia | `auth.handler(context.request)` | Direct |
| TanStack Start | `auth.handler(request)` | Direct |
| Expo | `auth.handler` | Direct |

## Available Plugins

Better Auth includes 40+ plugins:

### Authentication Methods
- `twoFactor` - 2FA with TOTP/OTP/backup codes
- `passkey` - WebAuthn/FIDO2 passkeys
- `magicLink` - Passwordless email links
- `emailOTP` - Email one-time passwords
- `phoneNumber` - Phone authentication
- `username` - Username-based auth
- `anonymous` - Anonymous sessions

### Access Control
- `admin` - Admin user management
- `organization` - Multi-tenant organizations
- `apiKey` - API key authentication
- `bearer` - Bearer token auth

### OAuth/SSO
- `genericOAuth` - Custom OAuth providers
- `sso` - Single Sign-On (SAML)
- `oauthProvider` - OAuth 2.1 server
- `oidcProvider` - OpenID Connect server

### Security
- `captcha` - CAPTCHA verification
- `haveIBeenPwned` - Password breach check

### Utilities
- `multiSession` - Multiple simultaneous sessions
- `oneTap` - Google One Tap
- `jwt` - JWT token generation
- `customSession` - Custom session data

## Reference Files

This skill includes comprehensive documentation in `references/`:

- **installation.md** - Installation and setup guide
- **basic-usage.md** - Core authentication patterns
- **database.md** - Database configuration and schema
- **session-management.md** - Session handling and caching
- **plugins.md** - Plugin system documentation
- **nextjs-integration.md** - Next.js specific guide
- **social-providers.md** - OAuth provider setup
- **two-factor.md** - 2FA implementation guide
- **options.md** - Complete configuration reference

Use `view` to read specific reference files when detailed information is needed.

## Working with This Skill

### For Beginners
Start with the installation and basic-usage reference files for foundational concepts.

### For Specific Features
Use the appropriate category reference file (plugins, database, etc.) for detailed information.

### For Code Examples
The quick reference section above contains common patterns extracted from the official docs.

## Resources

- **Official Docs**: https://www.better-auth.com/docs
- **LLMs.txt**: https://better-auth.com/llms.txt
- **GitHub**: https://github.com/better-auth/better-auth
- **MCP Server**: https://mcp.chonkie.ai/better-auth/better-auth-builder/mcp

## Notes

- This skill was generated from Better Auth's official documentation
- Better Auth requires TypeScript and Node.js 18+
- Database migrations should be run after adding plugins
- Session cookies use secure defaults in production
- The `nextCookies` plugin is required for Next.js server actions
