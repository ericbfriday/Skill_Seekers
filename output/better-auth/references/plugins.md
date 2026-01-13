# Better Auth Plugins

## Table of Contents

1. [Using Plugins](#using-plugins)
2. [Available Plugins](#available-plugins)
3. [Creating a Plugin](#creating-a-plugin)
4. [Server Plugin API](#server-plugin-api)
5. [Client Plugin API](#client-plugin-api)

---

## Using Plugins

Plugins extend Better Auth with additional features. Most plugins require both server and client configuration.

### Server Configuration

```typescript
// auth.ts
import { betterAuth } from "better-auth";
import { twoFactor, admin, organization } from "better-auth/plugins";

export const auth = betterAuth({
    plugins: [
        twoFactor(),
        admin(),
        organization(),
    ],
});
```

### Client Configuration

```typescript
// auth-client.ts
import { createAuthClient } from "better-auth/client";
import { 
    twoFactorClient, 
    adminClient, 
    organizationClient 
} from "better-auth/client/plugins";

const authClient = createAuthClient({
    plugins: [
        twoFactorClient(),
        adminClient(),
        organizationClient(),
    ],
});
```

### After Adding Plugins

Run database migration to add required tables:

```bash
npx @better-auth/cli generate
# or
npx @better-auth/cli migrate
```

---

## Available Plugins

### Authentication Methods

#### Two-Factor Authentication (2FA)
TOTP, OTP, backup codes, trusted devices.

```typescript
import { twoFactor } from "better-auth/plugins";
import { twoFactorClient } from "better-auth/client/plugins";

// Server
twoFactor({
    otpOptions: {
        sendOTP: async ({ user, otp }) => { /* send OTP */ },
    },
})

// Client
twoFactorClient({
    onTwoFactorRedirect() { /* redirect to 2FA page */ },
})
```

#### Passkey
WebAuthn/FIDO2 passwordless authentication.

```typescript
import { passkey } from "better-auth/plugins";
import { passkeyClient } from "better-auth/client/plugins";

// Server
passkey()

// Client
passkeyClient()

// Usage
await authClient.passkey.register();
await authClient.passkey.authenticate();
```

#### Magic Link
Passwordless email links.

```typescript
import { magicLink } from "better-auth/plugins";
import { magicLinkClient } from "better-auth/client/plugins";

// Server
magicLink({
    sendMagicLink: async ({ email, url }) => {
        await sendEmail(email, { body: `Login: ${url}` });
    },
})

// Client
await authClient.signIn.magicLink({ email: "user@example.com" });
```

#### Email OTP
One-time passwords via email.

```typescript
import { emailOTP } from "better-auth/plugins";
import { emailOTPClient } from "better-auth/client/plugins";

// Server
emailOTP({
    sendVerificationOTP: async ({ email, otp, type }) => {
        await sendEmail(email, { body: `Your code: ${otp}` });
    },
})

// Client
await authClient.emailOtp.sendVerificationOtp({ email: "..." });
await authClient.emailOtp.verifyEmail({ email: "...", otp: "123456" });
```

#### Phone Number
Phone-based authentication with OTP.

```typescript
import { phoneNumber } from "better-auth/plugins";
import { phoneNumberClient } from "better-auth/client/plugins";

// Server
phoneNumber({
    sendOTP: ({ phoneNumber, code }, ctx) => {
        // Send SMS
    },
})

// Client
await authClient.phoneNumber.sendOtp({ phoneNumber: "+1234567890" });
await authClient.phoneNumber.verifyOtp({ phoneNumber: "...", code: "123456" });
```

#### Username
Username-based authentication.

```typescript
import { username } from "better-auth/plugins";
import { usernameClient } from "better-auth/client/plugins";

// Server
username()

// Client
usernameClient()

// Usage
await authClient.signUp.username({
    username: "johndoe",
    password: "password",
});
await authClient.signIn.username({
    username: "johndoe",
    password: "password",
});
```

#### Anonymous
Anonymous session support.

```typescript
import { anonymous } from "better-auth/plugins";
import { anonymousClient } from "better-auth/client/plugins";

// Server
anonymous()

// Client
await authClient.signIn.anonymous();
await authClient.anonymous.linkAccount({ /* credentials */ });
```

### Access Control

#### Admin
User management and admin features.

```typescript
import { admin } from "better-auth/plugins";
import { adminClient } from "better-auth/client/plugins";

// Server
admin()

// Client
adminClient()

// Usage
await authClient.admin.listUsers();
await authClient.admin.createUser({ ... });
await authClient.admin.banUser({ userId: "..." });
await authClient.admin.unbanUser({ userId: "..." });
```

#### Organization
Multi-tenant organizations with teams and roles.

```typescript
import { organization } from "better-auth/plugins";
import { organizationClient } from "better-auth/client/plugins";

// Server
organization()

// Client
organizationClient()

// Usage
await authClient.organization.create({ name: "My Org" });
await authClient.organization.inviteMember({ email: "...", role: "member" });
await authClient.organization.createTeam({ name: "Engineering" });
```

#### API Key
API key authentication for services.

```typescript
import { apiKey } from "better-auth/plugins";
import { apiKeyClient } from "better-auth/client/plugins";

// Server
apiKey()

// Client
await authClient.apiKey.create({ name: "My API Key" });
await authClient.apiKey.list();
await authClient.apiKey.revoke({ id: "..." });
```

#### Bearer Token
Bearer token authentication for APIs.

```typescript
import { bearer } from "better-auth/plugins";

// Server
bearer()

// Client - use token in Authorization header
// Authorization: Bearer <token>
```

### OAuth & Identity

#### Generic OAuth
Custom OAuth provider support.

```typescript
import { genericOAuth } from "better-auth/plugins";
import { genericOAuthClient } from "better-auth/client/plugins";

// Server
genericOAuth({
    config: [
        {
            providerId: "custom",
            clientId: "...",
            clientSecret: "...",
            authorizationUrl: "https://provider.com/oauth/authorize",
            tokenUrl: "https://provider.com/oauth/token",
            userInfoUrl: "https://provider.com/api/user",
        },
    ],
})

// Client
await authClient.signIn.oauth2({ providerId: "custom" });
```

#### SSO (Single Sign-On)
SAML-based enterprise SSO.

```typescript
import { sso } from "better-auth/plugins";
import { ssoClient } from "better-auth/client/plugins";

// Server
sso()

// Client
ssoClient()
```

#### OAuth Provider
Make your auth server an OAuth 2.1 provider.

```typescript
import { oauthProvider } from "better-auth/plugins";

// Server
oauthProvider()
```

#### OIDC Provider
OpenID Connect provider functionality.

```typescript
import { oidcProvider } from "better-auth/plugins";

// Server
oidcProvider()
```

### Security & Utilities

#### Captcha
CAPTCHA verification for sign-up/sign-in.

```typescript
import { captcha } from "better-auth/plugins";

// Server
captcha({
    provider: "recaptcha", // or "hcaptcha", "turnstile"
    secretKey: process.env.CAPTCHA_SECRET_KEY,
})
```

#### Have I Been Pwned
Check passwords against breach databases.

```typescript
import { haveIBeenPwned } from "better-auth/plugins";

// Server - automatically checks passwords
haveIBeenPwned()
```

#### Multi-Session
Support multiple simultaneous sessions.

```typescript
import { multiSession } from "better-auth/plugins";
import { multiSessionClient } from "better-auth/client/plugins";

// Server
multiSession()

// Client
multiSessionClient()

// Usage
await authClient.multiSession.listDeviceSessions();
await authClient.multiSession.revokeDeviceSession({ token: "..." });
```

#### One Tap
Google One Tap sign-in.

```typescript
import { oneTap } from "better-auth/plugins";
import { oneTapClient } from "better-auth/client/plugins";

// Server
oneTap()

// Client
oneTapClient()
```

#### JWT
Generate JWT tokens for external services.

```typescript
import { jwt } from "better-auth/plugins";

// Server
jwt({
    secret: process.env.JWT_SECRET,
})

// Access tokens via auth.api
```

#### Custom Session
Customize session response data.

```typescript
import { customSession } from "better-auth/plugins";
import { customSessionClient } from "better-auth/client/plugins";

// Server
customSession(async ({ user, session }) => {
    const roles = await getRoles(user.id);
    return {
        roles,
        user,
        session,
    };
})

// Client
customSessionClient<typeof auth>()
```

---

## Creating a Plugin

### Server Plugin

```typescript
import type { BetterAuthPlugin } from "better-auth";
import { createAuthEndpoint } from "better-auth/api";

export const myPlugin = (options?: { customOption?: string }) => {
    return {
        id: "my-plugin",

        // Add database schema
        schema: {
            myTable: {
                fields: {
                    name: { type: "string" },
                    userId: {
                        type: "string",
                        references: { model: "user", field: "id" },
                    },
                },
            },
        },

        // Add endpoints
        endpoints: {
            getHelloWorld: createAuthEndpoint(
                "/my-plugin/hello-world",
                { method: "GET" },
                async (ctx) => {
                    return ctx.json({ message: "Hello World" });
                }
            ),
        },

        // Add middleware
        middlewares: [
            {
                path: "/my-plugin/*",
                middleware: createAuthMiddleware(async (ctx) => {
                    // Middleware logic
                }),
            },
        ],

        // Add hooks
        hooks: {
            before: [{
                matcher: (ctx) => ctx.path === "/sign-up/email",
                handler: createAuthMiddleware(async (ctx) => {
                    // Before sign-up logic
                }),
            }],
            after: [{
                matcher: (ctx) => ctx.path === "/sign-up/email",
                handler: createAuthMiddleware(async (ctx) => {
                    // After sign-up logic
                }),
            }],
        },

        // Rate limiting
        rateLimit: [
            {
                pathMatcher: (path) => path === "/my-plugin/hello-world",
                limit: 10,
                window: 60,
            },
        ],
    } satisfies BetterAuthPlugin;
};
```

### Client Plugin

```typescript
import type { BetterAuthClientPlugin } from "better-auth/client";
import type { myPlugin } from "./plugin";

export const myPluginClient = () => {
    return {
        id: "my-plugin",
        
        // Infer endpoints from server plugin
        $InferServerPlugin: {} as ReturnType<typeof myPlugin>,

        // Add custom actions
        getActions: ($fetch) => ({
            customAction: async (data: { foo: string }) => {
                return $fetch("/my-plugin/custom", {
                    method: "POST",
                    body: data,
                });
            },
        }),

        // Add reactive atoms (for hooks)
        getAtoms: ($fetch) => {
            const myAtom = atom<null>();
            return { myAtom };
        },
    } satisfies BetterAuthClientPlugin;
};
```

---

## Server Plugin API

### Context Object

Available in endpoint handlers and middleware:

```typescript
ctx.context.appName        // Application name
ctx.context.options        // Auth configuration
ctx.context.tables         // Table definitions
ctx.context.baseURL        // Auth server base URL
ctx.context.session        // Session configuration
ctx.context.secret         // Secret key
ctx.context.authCookie     // Cookie configuration
ctx.context.logger         // Logger instance
ctx.context.db             // Kysely database instance
ctx.context.adapter        // ORM-like database adapter
ctx.context.internalAdapter // Internal database calls
ctx.context.createAuthCookie // Cookie helper
ctx.context.trustedOrigins // Trusted origins list
ctx.context.isTrustedOrigin // Origin validation helper
```

### Helper Functions

```typescript
import { getSessionFromCtx, sessionMiddleware } from "better-auth/api";

// Get session in middleware
const session = await getSessionFromCtx(ctx);

// Require session for endpoint
createAuthEndpoint("/protected", {
    method: "GET",
    use: [sessionMiddleware],
}, async (ctx) => {
    const session = ctx.context.session; // Guaranteed to exist
});
```

### Endpoint Rules

1. Use kebab-case for paths
2. Only use `POST` or `GET` methods
3. `POST` for mutations, `GET` for queries
4. Prefix paths with plugin name to avoid conflicts

---

## Client Plugin API

### Path Methods

Override HTTP method for inferred endpoints:

```typescript
{
    pathMethods: {
        "/my-plugin/action": "POST",
    },
}
```

### Fetch Plugins

Add better-fetch plugins:

```typescript
{
    fetchPlugins: [
        // better-fetch plugins
    ],
}
```

### Atom Listeners

React to atom changes:

```typescript
{
    atomListeners: [
        {
            matcher: (key) => key === "session",
            listener: (value) => {
                // Handle session changes
            },
        },
    ],
}
```
