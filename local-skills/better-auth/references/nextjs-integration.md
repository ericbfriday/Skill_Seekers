# Better Auth Next.js Integration

## Table of Contents

1. [Create API Route](#create-api-route)
2. [Create Client](#create-client)
3. [RSC and Server Actions](#rsc-and-server-actions)
4. [Server Action Cookies](#server-action-cookies)
5. [Auth Protection](#auth-protection)
6. [Next.js 16 Compatibility](#nextjs-16-compatibility)

---

## Create API Route

Mount the Better Auth handler to an API route.

### App Router

```typescript
// app/api/auth/[...all]/route.ts
import { auth } from "@/lib/auth";
import { toNextJsHandler } from "better-auth/next-js";

export const { GET, POST } = toNextJsHandler(auth);
```

### Pages Router

```typescript
// pages/api/auth/[...all].ts
import { toNodeHandler } from "better-auth/node";
import { auth } from "@/lib/auth";

// Disallow body parsing, Better Auth handles it
export const config = { api: { bodyParser: false } };

export default toNodeHandler(auth.handler);
```

> **Tip**: You can change the path in your Better Auth configuration, but `/api/auth/[...all]` is recommended.

---

## Create Client

Create a client instance for React:

```typescript
// lib/auth-client.ts
import { createAuthClient } from "better-auth/react";

export const authClient = createAuthClient({
    // Optional: specify base URL if different domain
    // baseURL: "http://localhost:3000"
});

// Export specific methods for convenience
export const { signIn, signUp, useSession } = authClient;
```

The client uses:
- [nanostores](https://github.com/nanostores/nanostores) for reactive state
- [better-fetch](https://github.com/bekacru/better-fetch) for requests

---

## RSC and Server Actions

The `api` object on the auth instance contains all server-side actions. Every endpoint (including plugin endpoints) is invocable as a function.

### Getting Session in Server Action

```typescript
// server.ts
import { auth } from "@/lib/auth";
import { headers } from "next/headers";

const someAuthenticatedAction = async () => {
    "use server";
    const session = await auth.api.getSession({
        headers: await headers(),
    });
    
    if (!session) {
        throw new Error("Unauthorized");
    }
    
    // ... perform action
};
```

### Getting Session in RSC (React Server Component)

```tsx
// app/dashboard/page.tsx
import { auth } from "@/lib/auth";
import { headers } from "next/headers";

export default async function DashboardPage() {
    const session = await auth.api.getSession({
        headers: await headers(),
    });

    if (!session) {
        return <div>Not authenticated</div>;
    }

    return (
        <div>
            <h1>Welcome {session.user.name}</h1>
        </div>
    );
}
```

> **Note**: RSCs cannot set cookies, so [cookie cache](session-management.md#cookie-cache) won't refresh until the server is interacted with from the client via Server Actions or Route Handlers.

---

## Server Action Cookies

When calling functions that set cookies (like `signInEmail` or `signUpEmail`) in server actions, cookies won't be set automatically because server actions need the Next.js `cookies` helper.

### Solution: nextCookies Plugin

```typescript
// auth.ts
import { betterAuth } from "better-auth";
import { nextCookies } from "better-auth/next-js";

export const auth = betterAuth({
    // ... your config
    plugins: [nextCookies()], // MUST be the last plugin
});
```

Now cookies are automatically set when calling auth methods in server actions:

```typescript
// actions.ts
"use server";
import { auth } from "@/lib/auth";

const signIn = async (email: string, password: string) => {
    await auth.api.signInEmail({
        body: {
            email,
            password,
        },
    });
    // Cookies are automatically set!
};
```

---

## Auth Protection

### Recommended: Check in Each Page/Route

The most secure approach is validating the session in each protected page:

```tsx
// app/dashboard/page.tsx
import { auth } from "@/lib/auth";
import { headers } from "next/headers";
import { redirect } from "next/navigation";

export default async function DashboardPage() {
    const session = await auth.api.getSession({
        headers: await headers(),
    });

    if (!session) {
        redirect("/sign-in");
    }

    return <h1>Welcome {session.user.name}</h1>;
}
```

### Middleware: Cookie-Based Checks (Optimistic)

For optimistic redirects in middleware, check cookie existence (not validation):

```typescript
// middleware.ts
import { NextRequest, NextResponse } from "next/server";
import { getSessionCookie } from "better-auth/cookies";

export async function middleware(request: NextRequest) {
    const sessionCookie = getSessionCookie(request);

    // This is NOT secure - only checks cookie existence
    if (!sessionCookie) {
        return NextResponse.redirect(new URL("/login", request.url));
    }

    return NextResponse.next();
}

export const config = {
    matcher: ["/dashboard/:path*"],
};
```

> **Security Warning**: `getSessionCookie` only checks for cookie existence, NOT validity. Anyone can create a fake cookie. Always validate sessions on the server for protected actions.

### Middleware: Custom Cookie Configuration

If you customized cookie name or prefix:

```typescript
const sessionCookie = getSessionCookie(request, {
    cookieName: "my_session_cookie",
    cookiePrefix: "my_prefix",
});
```

### Middleware: Using Cookie Cache

```typescript
import { getCookieCache } from "better-auth/cookies";

export async function middleware(request: NextRequest) {
    const session = await getCookieCache(request);
    if (!session) {
        return NextResponse.redirect(new URL("/sign-in", request.url));
    }
    return NextResponse.next();
}
```

### Next.js 15.2.0+: Node.js Runtime Middleware

From Next.js 15.2.0, you can use Node.js runtime for full session validation:

```typescript
// middleware.ts
import { NextRequest, NextResponse } from "next/server";
import { headers } from "next/headers";
import { auth } from "@/lib/auth";

export async function middleware(request: NextRequest) {
    const session = await auth.api.getSession({
        headers: await headers(),
    });

    if (!session) {
        return NextResponse.redirect(new URL("/sign-in", request.url));
    }

    return NextResponse.next();
}

export const config = {
    runtime: "nodejs", // Required for auth.api calls
    matcher: ["/dashboard"],
};
```

> **Warning**: Node.js runtime in middleware is experimental before Next.js 16.

### Next.js 13-15.1.x: Fetch Session via API

For older Next.js versions (Edge Runtime middleware), fetch the session via HTTP:

```typescript
// middleware.ts
import { betterFetch } from "@better-fetch/fetch";
import type { auth } from "@/lib/auth";
import { NextRequest, NextResponse } from "next/server";

type Session = typeof auth.$Infer.Session;

export async function middleware(request: NextRequest) {
    const { data: session } = await betterFetch<Session>(
        "/api/auth/get-session",
        {
            baseURL: request.nextUrl.origin,
            headers: {
                cookie: request.headers.get("cookie") || "",
            },
        }
    );

    if (!session) {
        return NextResponse.redirect(new URL("/sign-in", request.url));
    }

    return NextResponse.next();
}

export const config = {
    matcher: ["/dashboard"],
};
```

---

## Next.js 16 Compatibility

Better Auth is fully compatible with Next.js 16. The main change is "middleware" is now called "proxy".

### Next.js 16+ Proxy

```typescript
// proxy.ts
import { NextRequest, NextResponse } from "next/server";
import { headers } from "next/headers";
import { auth } from "@/lib/auth";

export async function proxy(request: NextRequest) {
    const session = await auth.api.getSession({
        headers: await headers(),
    });

    if (!session) {
        return NextResponse.redirect(new URL("/sign-in", request.url));
    }

    return NextResponse.next();
}

export const config = {
    matcher: ["/dashboard"],
};
```

### Cookie-Only Check in Proxy

```typescript
// proxy.ts
import { NextRequest, NextResponse } from "next/server";
import { getSessionCookie } from "better-auth/cookies";

export async function proxy(request: NextRequest) {
    const sessionCookie = getSessionCookie(request);

    if (!sessionCookie) {
        return NextResponse.redirect(new URL("/", request.url));
    }

    return NextResponse.next();
}

export const config = {
    matcher: ["/dashboard"],
};
```

### Migration from Middleware to Proxy

Use the Next.js codemod for automatic migration:

```bash
npx @next/codemod@canary middleware-to-proxy .
```

Or manually:
1. Rename `middleware.ts` → `proxy.ts`
2. Change function name: `middleware` → `proxy`

All Better Auth methods work identically in both.

---

## Complete Example

### Auth Configuration

```typescript
// lib/auth.ts
import { betterAuth } from "better-auth";
import { nextCookies } from "better-auth/next-js";
import { Pool } from "pg";

export const auth = betterAuth({
    database: new Pool({
        connectionString: process.env.DATABASE_URL,
    }),
    emailAndPassword: {
        enabled: true,
    },
    socialProviders: {
        github: {
            clientId: process.env.GITHUB_CLIENT_ID!,
            clientSecret: process.env.GITHUB_CLIENT_SECRET!,
        },
    },
    plugins: [nextCookies()],
});
```

### Client Configuration

```typescript
// lib/auth-client.ts
import { createAuthClient } from "better-auth/react";

export const authClient = createAuthClient();

export const { signIn, signUp, signOut, useSession } = authClient;
```

### API Route

```typescript
// app/api/auth/[...all]/route.ts
import { auth } from "@/lib/auth";
import { toNextJsHandler } from "better-auth/next-js";

export const { GET, POST } = toNextJsHandler(auth);
```

### Protected Page

```tsx
// app/dashboard/page.tsx
import { auth } from "@/lib/auth";
import { headers } from "next/headers";
import { redirect } from "next/navigation";

export default async function DashboardPage() {
    const session = await auth.api.getSession({
        headers: await headers(),
    });

    if (!session) {
        redirect("/sign-in");
    }

    return (
        <main>
            <h1>Dashboard</h1>
            <p>Welcome, {session.user.name}!</p>
            <p>Email: {session.user.email}</p>
        </main>
    );
}
```

### Sign In Page

```tsx
// app/sign-in/page.tsx
"use client";
import { signIn } from "@/lib/auth-client";
import { useState } from "react";
import { useRouter } from "next/navigation";

export default function SignInPage() {
    const router = useRouter();
    const [email, setEmail] = useState("");
    const [password, setPassword] = useState("");
    const [error, setError] = useState("");

    const handleSubmit = async (e: React.FormEvent) => {
        e.preventDefault();
        const { error } = await signIn.email({
            email,
            password,
            callbackURL: "/dashboard",
        });
        if (error) {
            setError(error.message);
        }
    };

    return (
        <form onSubmit={handleSubmit}>
            <input
                type="email"
                value={email}
                onChange={(e) => setEmail(e.target.value)}
                placeholder="Email"
            />
            <input
                type="password"
                value={password}
                onChange={(e) => setPassword(e.target.value)}
                placeholder="Password"
            />
            {error && <p className="error">{error}</p>}
            <button type="submit">Sign In</button>
        </form>
    );
}
```

### Middleware

```typescript
// middleware.ts
import { NextRequest, NextResponse } from "next/server";
import { getSessionCookie } from "better-auth/cookies";

export async function middleware(request: NextRequest) {
    const sessionCookie = getSessionCookie(request);
    const { pathname } = request.nextUrl;

    // Redirect authenticated users from auth pages
    if (sessionCookie && ["/sign-in", "/sign-up"].includes(pathname)) {
        return NextResponse.redirect(new URL("/dashboard", request.url));
    }

    // Redirect unauthenticated users from protected pages
    if (!sessionCookie && pathname.startsWith("/dashboard")) {
        return NextResponse.redirect(new URL("/sign-in", request.url));
    }

    return NextResponse.next();
}

export const config = {
    matcher: ["/dashboard/:path*", "/sign-in", "/sign-up"],
};
```
