# Better Auth Documentation Index

## Table of Contents

1. [Installation & Setup](#installation--setup)
2. [Basic Usage](#basic-usage)
3. [Database Configuration](#database-configuration)
4. [Session Management](#session-management)
5. [Social Providers](#social-providers)
6. [Two-Factor Authentication](#two-factor-authentication)
7. [Framework Integrations](#framework-integrations)
8. [Plugins](#plugins)
9. [Configuration Options](#configuration-options)

---

## Reference Files

| File | Description |
|------|-------------|
| [installation.md](installation.md) | Package installation, environment setup, database configuration |
| [basic-usage.md](basic-usage.md) | Email/password auth, social sign-in, sessions, signout |
| [database.md](database.md) | Database adapters, schema, migrations, hooks |
| [session-management.md](session-management.md) | Session expiration, caching, revocation, stateless mode |
| [social-providers.md](social-providers.md) | OAuth configuration for 30+ providers |
| [two-factor.md](two-factor.md) | TOTP, OTP, backup codes, trusted devices |
| [nextjs-integration.md](nextjs-integration.md) | Next.js App Router, Pages Router, middleware, RSC |
| [plugins.md](plugins.md) | Plugin system, creating plugins, available plugins |
| [options.md](options.md) | Complete configuration reference |

---

## Quick Links

### Getting Started
- [Installation Guide](installation.md#install-the-package)
- [Environment Variables](installation.md#set-environment-variables)
- [Create Auth Instance](installation.md#create-a-better-auth-instance)
- [Database Setup](installation.md#configure-database)

### Authentication
- [Email/Password Sign Up](basic-usage.md#sign-up)
- [Email/Password Sign In](basic-usage.md#sign-in)
- [Social OAuth](basic-usage.md#social-sign-on)
- [Magic Links](plugins.md#magic-link)
- [Passkeys](plugins.md#passkey)

### Session & Security
- [Session Configuration](session-management.md#session-expiration)
- [Cookie Caching](session-management.md#cookie-cache)
- [Two-Factor Auth](two-factor.md)
- [Rate Limiting](options.md#ratelimit)

### Database
- [PostgreSQL Setup](database.md#postgresql)
- [Prisma Adapter](database.md#prisma)
- [Drizzle Adapter](database.md#drizzle)
- [MongoDB Adapter](database.md#mongodb)
- [Schema Reference](database.md#core-schema)

### Framework Integration
- [Next.js](nextjs-integration.md)
- [Nuxt](installation.md#nuxt)
- [SvelteKit](installation.md#svelte-kit)
- [Remix](installation.md#remix)
- [Astro](installation.md#astro)
- [Hono](installation.md#hono)
- [Express](installation.md#express)

### Advanced
- [Custom Plugins](plugins.md#creating-a-plugin)
- [Database Hooks](database.md#database-hooks)
- [Custom Session Data](session-management.md#customizing-session-response)
- [Stateless Sessions](session-management.md#stateless-session-management)

---

## Supported Social Providers

Better Auth supports 35+ social OAuth providers:

| Provider | Docs Link |
|----------|-----------|
| Apple | [social-providers.md#apple](social-providers.md#apple) |
| Discord | [social-providers.md#discord](social-providers.md#discord) |
| Facebook | [social-providers.md#facebook](social-providers.md#facebook) |
| GitHub | [social-providers.md#github](social-providers.md#github) |
| Google | [social-providers.md#google](social-providers.md#google) |
| LinkedIn | [social-providers.md#linkedin](social-providers.md#linkedin) |
| Microsoft | [social-providers.md#microsoft](social-providers.md#microsoft) |
| Slack | [social-providers.md#slack](social-providers.md#slack) |
| Spotify | [social-providers.md#spotify](social-providers.md#spotify) |
| TikTok | [social-providers.md#tiktok](social-providers.md#tiktok) |
| Twitch | [social-providers.md#twitch](social-providers.md#twitch) |
| Twitter/X | [social-providers.md#twitter](social-providers.md#twitter) |

And many more: Atlassian, Cognito, Dropbox, Figma, GitLab, Hugging Face, Kakao, Kick, LINE, Linear, Naver, Notion, PayPal, Polar, Reddit, Roblox, Salesforce, Vercel, VK, Zoom...

---

## Plugin Categories

### Authentication Methods
- Two-Factor (2FA)
- Passkey
- Magic Link
- Email OTP
- Phone Number
- Username
- Anonymous

### Access Control
- Admin
- Organization
- API Key
- Bearer Token

### OAuth/Identity
- Generic OAuth
- SSO (SAML)
- OAuth Provider
- OIDC Provider

### Security & Utilities
- Captcha
- Have I Been Pwned
- Multi-Session
- One Tap
- JWT

---

## External Resources

- **Official Documentation**: https://www.better-auth.com/docs
- **LLMs.txt**: https://better-auth.com/llms.txt
- **GitHub Repository**: https://github.com/better-auth/better-auth
- **MCP Server**: https://mcp.chonkie.ai/better-auth/better-auth-builder/mcp
- **NPM Package**: https://www.npmjs.com/package/better-auth
