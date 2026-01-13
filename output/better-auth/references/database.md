# Better Auth Database Configuration

## Table of Contents

1. [Database Adapters](#database-adapters)
2. [CLI Commands](#cli-commands)
3. [Core Schema](#core-schema)
4. [Custom Tables](#custom-tables)
5. [ID Generation](#id-generation)
6. [Database Hooks](#database-hooks)
7. [Secondary Storage](#secondary-storage)
8. [Programmatic Migrations](#programmatic-migrations)

---

## Database Adapters

Better Auth supports multiple database configurations.

### Direct Connection

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
        host: "localhost",
        user: "root",
        database: "myapp",
    }),
});
```

### ORM Adapters

#### Drizzle ORM

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
import { MongoClient } from "mongodb";

const client = new MongoClient(process.env.MONGODB_URI!);

export const auth = betterAuth({
    database: mongodbAdapter(client),
});
```

---

## CLI Commands

### Generate Schema

Creates schema for your ORM or generates SQL file:

```bash
npx @better-auth/cli generate
```

### Migrate (Kysely Only)

Creates tables directly in the database:

```bash
npx @better-auth/cli migrate
```

> **Note**: For PostgreSQL with non-default schemas, the migrate command automatically detects your `search_path` configuration.

---

## Core Schema

Better Auth requires these tables:

### User Table

| Field | Type | Description |
|-------|------|-------------|
| id | string | Primary key |
| name | string | Display name |
| email | string | Email address |
| emailVerified | boolean | Email verification status |
| image | string (optional) | Avatar URL |
| createdAt | Date | Creation timestamp |
| updatedAt | Date | Update timestamp |

### Session Table

| Field | Type | Description |
|-------|------|-------------|
| id | string | Primary key |
| userId | string | Foreign key to user |
| token | string | Unique session token |
| expiresAt | Date | Expiration time |
| ipAddress | string (optional) | Client IP |
| userAgent | string (optional) | Client user agent |
| createdAt | Date | Creation timestamp |
| updatedAt | Date | Update timestamp |

### Account Table

| Field | Type | Description |
|-------|------|-------------|
| id | string | Primary key |
| userId | string | Foreign key to user |
| accountId | string | Provider's user ID |
| providerId | string | Provider identifier |
| accessToken | string (optional) | OAuth access token |
| refreshToken | string (optional) | OAuth refresh token |
| accessTokenExpiresAt | Date (optional) | Token expiration |
| refreshTokenExpiresAt | Date (optional) | Refresh token expiration |
| scope | string (optional) | OAuth scope |
| idToken | string (optional) | OIDC ID token |
| password | string (optional) | Hashed password |
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

---

## Custom Tables

### Custom Table Names

```typescript
export const auth = betterAuth({
    user: {
        modelName: "users",
        fields: {
            name: "full_name",
            email: "email_address",
        },
    },
    session: {
        modelName: "user_sessions",
        fields: {
            userId: "user_id",
        },
    },
});
```

> **Note**: Type inference still uses original field names (`user.name`, not `user.full_name`).

### Plugin Schema Customization

```typescript
import { betterAuth } from "better-auth";
import { twoFactor } from "better-auth/plugins";

export const auth = betterAuth({
    plugins: [
        twoFactor({
            schema: {
                user: {
                    fields: {
                        twoFactorEnabled: "two_factor_enabled",
                        secret: "two_factor_secret",
                    },
                },
            },
        }),
    ],
});
```

### Additional Fields

Add custom fields to user or session tables:

```typescript
export const auth = betterAuth({
    user: {
        additionalFields: {
            role: {
                type: ["user", "admin"],
                required: false,
                defaultValue: "user",
                input: false, // don't allow user to set during signup
            },
            lang: {
                type: "string",
                required: false,
                defaultValue: "en",
            },
            age: {
                type: "number",
                required: false,
            },
        },
    },
});
```

**Field Properties:**
- `type`: `"string"` | `"number"` | `"boolean"` | `"date"` | `string[]` (enum)
- `required`: Required for new records (default: `false`)
- `defaultValue`: Default value (JS layer only, DB field is optional)
- `input`: Allow value during creation (default: `true`)

**Usage:**
```typescript
const res = await auth.api.signUpEmail({
    email: "test@example.com",
    password: "password",
    name: "John Doe",
    lang: "fr", // custom field
});

console.log(res.user.role); // "user" (default)
console.log(res.user.lang); // "fr"
```

### Mapping Social Profile to User

Populate additional fields from OAuth profile:

```typescript
export const auth = betterAuth({
    user: {
        additionalFields: {
            firstName: { type: "string" },
            lastName: { type: "string" },
        },
    },
    socialProviders: {
        github: {
            clientId: process.env.GITHUB_CLIENT_ID!,
            clientSecret: process.env.GITHUB_CLIENT_SECRET!,
            mapProfileToUser: (profile) => ({
                firstName: profile.name.split(" ")[0],
                lastName: profile.name.split(" ")[1],
            }),
        },
        google: {
            clientId: process.env.GOOGLE_CLIENT_ID!,
            clientSecret: process.env.GOOGLE_CLIENT_SECRET!,
            mapProfileToUser: (profile) => ({
                firstName: profile.given_name,
                lastName: profile.family_name,
            }),
        },
    },
});
```

---

## ID Generation

### Default Behavior

Better Auth generates unique IDs by default.

### Let Database Generate IDs

```typescript
export const auth = betterAuth({
    database: db,
    advanced: {
        database: {
            generateId: false, // database handles all IDs
        },
    },
});
```

### Serial (Auto-Increment) IDs

```typescript
export const auth = betterAuth({
    database: db,
    advanced: {
        database: {
            generateId: "serial",
        },
    },
});
```

> **Note**: IDs are still returned as strings but represent numbers.

### UUID IDs

```typescript
export const auth = betterAuth({
    database: db,
    advanced: {
        database: {
            generateId: "uuid",
        },
    },
});
```

### Custom ID Generator

```typescript
export const auth = betterAuth({
    database: db,
    advanced: {
        database: {
            generateId: (options) => {
                if (options.model === "user") {
                    return false; // let database generate
                }
                return crypto.randomUUID(); // UUIDs for other tables
            },
        },
    },
});
```

### Mixed ID Types

Useful when migrating from other auth providers:

```typescript
export const auth = betterAuth({
    database: db,
    user: {
        modelName: "users", // uses serial primary key
    },
    session: {
        modelName: "session", // uses text primary key
    },
    advanced: {
        database: {
            generateId: (options) => {
                if (options.model === "user" || options.model === "users") {
                    return false; // database auto-increment
                }
                return crypto.randomUUID(); // UUIDs for session, account, etc.
            },
        },
    },
});
```

---

## Database Hooks

Execute custom logic during database operations.

### Hook Types

- **before**: Called before create/update/delete. Return `false` to abort.
- **after**: Called after successful operation.

### User Hooks

```typescript
export const auth = betterAuth({
    databaseHooks: {
        user: {
            create: {
                before: async (user, ctx) => {
                    // Modify user before creation
                    return {
                        data: {
                            ...user,
                            firstName: user.name.split(" ")[0],
                            lastName: user.name.split(" ")[1],
                        },
                    };
                },
                after: async (user) => {
                    // Create Stripe customer, send welcome email, etc.
                    await createStripeCustomer(user.id, user.email);
                },
            },
            update: {
                before: async (data, ctx) => {
                    console.log("User update by:", ctx.context.session?.userId);
                    return { data };
                },
                after: async (user) => {
                    // Log update, sync with external service
                },
            },
            delete: {
                before: async (user, ctx) => {
                    if (user.email.includes("admin")) {
                        return false; // Prevent admin deletion
                    }
                    return true;
                },
                after: async (user) => {
                    // Cleanup related data
                },
            },
        },
    },
});
```

### Session Hooks

```typescript
export const auth = betterAuth({
    databaseHooks: {
        session: {
            delete: {
                before: async (session, ctx) => {
                    if (session.userId === "protected-user-id") {
                        return false; // Prevent session deletion
                    }
                    return true;
                },
                after: async (session) => {
                    console.log(`Session ${session.token} deleted`);
                },
            },
        },
    },
});
```

### Throwing Errors in Hooks

```typescript
import { APIError } from "better-auth/api";

export const auth = betterAuth({
    databaseHooks: {
        user: {
            create: {
                before: async (user, ctx) => {
                    if (!user.isAgreedToTerms) {
                        throw new APIError("BAD_REQUEST", {
                            message: "User must agree to TOS before signing up.",
                        });
                    }
                    return { data: user };
                },
            },
        },
    },
});
```

---

## Secondary Storage

Use key-value stores for sessions and rate limiting.

### Interface

```typescript
interface SecondaryStorage {
    get: (key: string) => Promise<unknown>;
    set: (key: string, value: string, ttl?: number) => Promise<void>;
    delete: (key: string) => Promise<void>;
}
```

### Redis Example

```typescript
import { createClient } from "redis";
import { betterAuth } from "better-auth";

const redis = createClient();
await redis.connect();

export const auth = betterAuth({
    secondaryStorage: {
        get: async (key) => {
            return await redis.get(key);
        },
        set: async (key, value, ttl) => {
            if (ttl) {
                await redis.set(key, value, { EX: ttl });
            } else {
                await redis.set(key, value);
            }
        },
        delete: async (key) => {
            await redis.del(key);
        },
    },
});
```

### Options

```typescript
export const auth = betterAuth({
    secondaryStorage: { /* ... */ },
    session: {
        storeSessionInDatabase: true, // also store in primary database
        preserveSessionInDatabase: true, // keep session on revocation
    },
});
```

---

## Programmatic Migrations

For serverless environments where CLI isn't practical:

```typescript
import { getMigrations } from "better-auth/db";

const { toBeCreated, toBeAdded, runMigrations, compileMigrations } = 
    await getMigrations(authConfig);

// Check what migrations are needed
console.log("Tables to create:", toBeCreated);
console.log("Fields to add:", toBeAdded);

// Run migrations
await runMigrations();

// Or get SQL to run manually
const sql = await compileMigrations();
console.log(sql);
```

### Cloudflare Workers Example

```typescript
// src/index.ts
import { Hono } from "hono";
import { getMigrations } from "better-auth/db";

const app = new Hono<{ Bindings: Env }>();

app.post("/migrate", async (c) => {
    const authConfig = {
        database: c.env.DB, // D1 database binding
        // ... rest of config
    };

    try {
        const { toBeCreated, toBeAdded, runMigrations } = 
            await getMigrations(authConfig);

        if (toBeCreated.length === 0 && toBeAdded.length === 0) {
            return c.json({ message: "No migrations needed" });
        }

        await runMigrations();

        return c.json({
            message: "Migrations completed",
            created: toBeCreated.map(t => t.table),
            added: toBeAdded.map(t => t.table),
        });
    } catch (error) {
        return c.json({ error: String(error) }, 500);
    }
});

export default app;
```

> **Note**: Programmatic migrations only work with built-in database adapters (SQLite/D1, PostgreSQL, MySQL, MSSQL), not Prisma or Drizzle.

---

## Experimental Joins

Enable database joins for improved performance (50+ endpoints support joins):

```typescript
export const auth = betterAuth({
    experimental: { 
        joins: true,
    },
});
```

Run CLI to generate required relationships:

```bash
npx @better-auth/cli migrate
# or
npx @better-auth/cli generate
```

See adapter-specific documentation for detailed join configuration.
