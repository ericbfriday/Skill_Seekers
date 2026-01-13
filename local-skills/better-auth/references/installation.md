# Better Auth Installation Guide

## Table of Contents

1. [Install the Package](#install-the-package)
2. [Set Environment Variables](#set-environment-variables)
3. [Create a Better Auth Instance](#create-a-better-auth-instance)
4. [Configure Database](#configure-database)
5. [Create Database Tables](#create-database-tables)
6. [Authentication Methods](#authentication-methods)
7. [Mount Handler](#mount-handler)
8. [Create Client Instance](#create-client-instance)

---

## Install the Package

```bash
# npm
npm install better-auth

# pnpm
pnpm add better-auth

# yarn
yarn add better-auth

# bun
bun add better-auth
```

> **Note**: If you're using a separate client and server setup, make sure to install Better Auth in both parts of your project.

---

## Set Environment Variables

Create a `.env` file in the root of your project:

### Secret Key

A secret value used for encryption and hashing. Must be at least 32 characters with high entropy.

```env
BETTER_AUTH_SECRET=your-secret-key-at-least-32-characters
```

Generate a secret:
```bash
openssl rand -base64 32
```

### Base URL

```env
BETTER_AUTH_URL=http://localhost:3000
```

---

## Create a Better Auth Instance

Create a file named `auth.ts` in one of these locations:
- Project root
- `lib/` folder
- `utils/` folder
- Nested under `src/`, `app/`, or `server/` (e.g., `src/lib/auth.ts`)

```typescript
// auth.ts
import { betterAuth } from "better-auth";

export const auth = betterAuth({
    // ... configuration
});
```

> **Important**: Export the auth instance with the variable name `auth` or as a `default` export.

---

## Configure Database

Better Auth requires a database to store user data. Choose your preferred method:

### Direct Database Connection

#### SQLite

```typescript
import { betterAuth } from "better-auth";
import Database from "better-sqlite3";

export const auth = betterAuth({
    database: new Database("./sqlite.db"),
});
```

#### PostgreSQL

```typescript
import { betterAuth } from "better-auth";
import { Pool } from "pg";

export const auth = betterAuth({
    database: new Pool({
        connectionString: process.env.DATABASE_URL,
    }),
});
```

#### MySQL

```typescript
import { betterAuth } from "better-auth";
import { createPool } from "mysql2/promise";

export const auth = betterAuth({
    database: createPool({
        // connection options
    }),
});
```

### ORM Adapters

#### Drizzle ORM

```typescript
import { betterAuth } from "better-auth";
import { drizzleAdapter } from "better-auth/adapters/drizzle";
import { db } from "@/db"; // your drizzle instance

export const auth = betterAuth({
    database: drizzleAdapter(db, {
        provider: "pg", // or "mysql", "sqlite"
    }),
});
```

#### Prisma

```typescript
import { betterAuth } from "better-auth";
import { prismaAdapter } from "better-auth/adapters/prisma";
import { PrismaClient } from "@prisma/client";

const prisma = new PrismaClient();

export const auth = betterAuth({
    database: prismaAdapter(prisma, {
        provider: "postgresql", // or "mysql", "sqlite"
    }),
});
```

#### MongoDB

```typescript
import { betterAuth } from "better-auth";
import { mongodbAdapter } from "better-auth/adapters/mongodb";
import { client } from "@/db"; // your mongodb client

export const auth = betterAuth({
    database: mongodbAdapter(client),
});
```

---

## Create Database Tables

Better Auth includes a CLI tool to manage database migrations.

### Generate Schema (for ORMs)

```bash
npx @better-auth/cli generate
```

This creates the schema required by Better Auth for your ORM (Prisma, Drizzle) or generates SQL for direct execution.

### Migrate (Kysely adapter only)

```bash
npx @better-auth/cli migrate
```

This creates tables directly in the database.

---

## Authentication Methods

Configure the authentication methods you want to use:

```typescript
import { betterAuth } from "better-auth";

export const auth = betterAuth({
    // ... database config
    emailAndPassword: {
        enabled: true,
    },
    socialProviders: {
        github: {
            clientId: process.env.GITHUB_CLIENT_ID as string,
            clientSecret: process.env.GITHUB_CLIENT_SECRET as string,
        },
        google: {
            clientId: process.env.GOOGLE_CLIENT_ID as string,
            clientSecret: process.env.GOOGLE_CLIENT_SECRET as string,
        },
    },
});
```

> **Tip**: Use plugins for additional authentication methods like [passkey](/docs/plugins/passkey), [username](/docs/plugins/username), [magic link](/docs/plugins/magic-link).

---

## Mount Handler

Create a route handler on your server for the path `/api/auth/*` (or custom base path).

### Next.js App Router

```typescript
// app/api/auth/[...all]/route.ts
import { auth } from "@/lib/auth";
import { toNextJsHandler } from "better-auth/next-js";

export const { POST, GET } = toNextJsHandler(auth);
```

### Next.js Pages Router

```typescript
// pages/api/auth/[...all].ts
import { auth } from "@/lib/auth";
import { toNodeHandler } from "better-auth/node";

export const config = { api: { bodyParser: false } };
export default toNodeHandler(auth.handler);
```

### Nuxt

```typescript
// server/api/auth/[...all].ts
import { auth } from "~/utils/auth";

export default defineEventHandler((event) => {
    return auth.handler(toWebRequest(event));
});
```

### SvelteKit

```typescript
// hooks.server.ts
import { auth } from "$lib/auth";
import { svelteKitHandler } from "better-auth/svelte-kit";
import { building } from '$app/environment';

export async function handle({ event, resolve }) {
    return svelteKitHandler({ event, resolve, auth, building });
}
```

### Remix

```typescript
// app/routes/api.auth.$.ts
import { auth } from '~/lib/auth.server';
import type { LoaderFunctionArgs, ActionFunctionArgs } from "@remix-run/node";

export async function loader({ request }: LoaderFunctionArgs) {
    return auth.handler(request);
}

export async function action({ request }: ActionFunctionArgs) {
    return auth.handler(request);
}
```

### Solid Start

```typescript
// routes/api/auth/*all.ts
import { auth } from "~/lib/auth";
import { toSolidStartHandler } from "better-auth/solid-start";

export const { GET, POST } = toSolidStartHandler(auth);
```

### Hono

```typescript
// src/index.ts
import { Hono } from "hono";
import { auth } from "./auth";
import { serve } from "@hono/node-server";

const app = new Hono();

app.on(["POST", "GET"], "/api/auth/*", (c) => auth.handler(c.req.raw));

serve(app);
```

### Express

```typescript
// server.ts
import express from "express";
import { toNodeHandler } from "better-auth/node";
import { auth } from "./auth";

const app = express();

// For Express v5, use: app.all('/api/auth/{*any}', toNodeHandler(auth));
app.all("/api/auth/*", toNodeHandler(auth));

// Mount express.json() AFTER Better Auth handler
app.use(express.json());

app.listen(8000);
```

### Astro

```typescript
// pages/api/auth/[...all].ts
import type { APIRoute } from "astro";
import { auth } from "@/auth";

export const GET: APIRoute = async (ctx) => {
    return auth.handler(ctx.request);
};

export const POST: APIRoute = async (ctx) => {
    return auth.handler(ctx.request);
};
```

### Elysia

```typescript
import { Elysia, Context } from "elysia";
import { auth } from "./auth";

const betterAuthView = (context: Context) => {
    const BETTER_AUTH_ACCEPT_METHODS = ["POST", "GET"];
    if (BETTER_AUTH_ACCEPT_METHODS.includes(context.request.method)) {
        return auth.handler(context.request);
    } else {
        context.error(405);
    }
};

const app = new Elysia().all("/api/auth/*", betterAuthView).listen(3000);
```

### TanStack Start

```typescript
// src/routes/api/auth/$.ts
import { createFileRoute } from '@tanstack/react-router';
import { auth } from '@/lib/auth/auth';

export const Route = createFileRoute('/api/auth/$')({
    server: {
        handlers: {
            GET: async ({ request }: { request: Request }) => {
                return await auth.handler(request);
            },
            POST: async ({ request }: { request: Request }) => {
                return await auth.handler(request);
            },
        },
    },
});
```

### Expo

```typescript
// app/api/auth/[...all]+api.ts
import { auth } from '@/lib/server/auth';

const handler = auth.handler;
export { handler as GET, handler as POST };
```

---

## Create Client Instance

The client-side library helps you interact with the auth server.

### React

```typescript
// lib/auth-client.ts
import { createAuthClient } from "better-auth/react";

export const authClient = createAuthClient({
    baseURL: "http://localhost:3000", // optional if same domain
});

// Export specific methods
export const { signIn, signUp, useSession } = authClient;
```

### Vue

```typescript
import { createAuthClient } from "better-auth/vue";

export const authClient = createAuthClient({
    baseURL: "http://localhost:3000",
});
```

### Svelte

```typescript
import { createAuthClient } from "better-auth/svelte";

export const authClient = createAuthClient({
    baseURL: "http://localhost:3000",
});
```

### Solid

```typescript
import { createAuthClient } from "better-auth/solid";

export const authClient = createAuthClient({
    baseURL: "http://localhost:3000",
});
```

### Vanilla JavaScript

```typescript
import { createAuthClient } from "better-auth/client";

export const authClient = createAuthClient({
    baseURL: "http://localhost:3000",
});
```

> **Note**: If using a different base path than `/api/auth`, pass the full URL including the path (e.g., `http://localhost:3000/custom-path/auth`).

---

## Next Steps

After setup, continue to:
- [Basic Usage](basic-usage.md) - Learn authentication patterns
- [Database Configuration](database.md) - Advanced database setup
- [Session Management](session-management.md) - Configure sessions
- [Plugins](plugins.md) - Add advanced features
