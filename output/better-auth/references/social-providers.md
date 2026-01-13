# Better Auth Social Providers

## Table of Contents

1. [Configuration](#configuration)
2. [Sign In Flow](#sign-in-flow)
3. [Available Providers](#available-providers)
4. [Provider Options](#provider-options)
5. [Account Linking](#account-linking)

---

## Configuration

Add social providers to your auth configuration:

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

---

## Sign In Flow

### Client-Side Redirect

```typescript
await authClient.signIn.social({
    provider: "github", // Provider ID
    callbackURL: "/dashboard", // Redirect after success
    errorCallbackURL: "/error", // Redirect on error
    newUserCallbackURL: "/welcome", // Redirect for new users
});
```

### Without Redirect

```typescript
const { url } = await authClient.signIn.social({
    provider: "google",
    disableRedirect: true, // Get URL instead of redirecting
});

// Manually redirect
window.location.href = url;
```

### Using ID Token (Mobile/Native Apps)

```typescript
await authClient.signIn.social({
    provider: "google",
    idToken: "google-id-token-from-sdk",
});
```

### Using Access Token

```typescript
await authClient.signIn.social({
    provider: "github",
    accessToken: "github-access-token",
});
```

---

## Available Providers

### Major Providers

| Provider | ID | Setup |
|----------|-----|-------|
| Apple | `apple` | [Developer Portal](https://developer.apple.com/) |
| Discord | `discord` | [Discord Developers](https://discord.com/developers/applications) |
| Facebook | `facebook` | [Meta Developers](https://developers.facebook.com/) |
| GitHub | `github` | [GitHub Settings](https://github.com/settings/developers) |
| Google | `google` | [Google Cloud Console](https://console.cloud.google.com/) |
| LinkedIn | `linkedin` | [LinkedIn Developers](https://www.linkedin.com/developers/) |
| Microsoft | `microsoft` | [Azure Portal](https://portal.azure.com/) |
| Slack | `slack` | [Slack API](https://api.slack.com/apps) |
| Spotify | `spotify` | [Spotify Dashboard](https://developer.spotify.com/dashboard) |
| TikTok | `tiktok` | [TikTok Developers](https://developers.tiktok.com/) |
| Twitch | `twitch` | [Twitch Developers](https://dev.twitch.tv/) |
| Twitter/X | `twitter` | [Twitter Developer Portal](https://developer.twitter.com/) |

### Additional Providers

| Provider | ID | Notes |
|----------|-----|-------|
| Atlassian | `atlassian` | Jira, Confluence, etc. |
| Cognito | `cognito` | AWS Cognito |
| Dropbox | `dropbox` | |
| Figma | `figma` | |
| GitLab | `gitlab` | |
| Hugging Face | `huggingface` | |
| Kakao | `kakao` | Korean social platform |
| Kick | `kick` | Streaming platform |
| LINE | `line` | Japanese messaging |
| Linear | `linear` | Project management |
| Naver | `naver` | Korean search engine |
| Notion | `notion` | |
| PayPal | `paypal` | |
| Polar | `polar` | |
| Reddit | `reddit` | |
| Roblox | `roblox` | |
| Salesforce | `salesforce` | |
| Vercel | `vercel` | |
| VK | `vk` | Russian social network |
| Zoom | `zoom` | |

### Generic OAuth

For unlisted providers, use the `genericOAuth` plugin:

```typescript
import { genericOAuth } from "better-auth/plugins";

export const auth = betterAuth({
    plugins: [
        genericOAuth({
            config: [
                {
                    providerId: "custom-provider",
                    clientId: process.env.CUSTOM_CLIENT_ID!,
                    clientSecret: process.env.CUSTOM_CLIENT_SECRET!,
                    authorizationUrl: "https://provider.com/oauth/authorize",
                    tokenUrl: "https://provider.com/oauth/token",
                    userInfoUrl: "https://provider.com/api/user",
                },
            ],
        }),
    ],
});
```

---

## Provider Options

Each provider accepts these options:

```typescript
socialProviders: {
    github: {
        // Required
        clientId: string,
        clientSecret: string,

        // Optional
        redirectURI: string, // Custom callback URL
        scope: string[], // Additional OAuth scopes
        prompt: "select_account" | "consent" | "login" | "none",
        responseMode: "query" | "form_post",

        // Behavior
        disableSignUp: boolean, // Prevent new user registration
        disableImplicitSignUp: boolean, // Prevent implicit sign-up
        overrideUserInfoOnSignIn: boolean, // Update user info on sign-in
        disableIdTokenSignIn: boolean, // Disable ID token auth

        // Custom handlers
        mapProfileToUser: (profile) => ({ /* user fields */ }),
        getUserInfo: async (token) => ({ /* user info */ }),
        refreshAccessToken: async (token) => ({ /* new tokens */ }),
        verifyIdToken: async (token) => ({ /* verified claims */ }),
    },
},
```

### Map Profile to User

Customize how provider profile maps to user fields:

```typescript
google: {
    clientId: process.env.GOOGLE_CLIENT_ID!,
    clientSecret: process.env.GOOGLE_CLIENT_SECRET!,
    mapProfileToUser: (profile) => ({
        firstName: profile.given_name,
        lastName: profile.family_name,
        locale: profile.locale,
    }),
},
```

### Custom Redirect URI

```typescript
google: {
    clientId: process.env.GOOGLE_CLIENT_ID!,
    clientSecret: process.env.GOOGLE_CLIENT_SECRET!,
    redirectURI: "https://myapp.com/auth/callback/google",
},
```

Default callback URL format: `{baseURL}/api/auth/callback/{provider}`

### Additional Scopes

```typescript
github: {
    clientId: process.env.GITHUB_CLIENT_ID!,
    clientSecret: process.env.GITHUB_CLIENT_SECRET!,
    scope: ["read:org", "repo"], // Additional scopes beyond default
},
```

---

## Account Linking

Configure how social accounts link to existing users:

```typescript
export const auth = betterAuth({
    account: {
        accountLinking: {
            enabled: true, // Enable account linking (default: true)
            trustedProviders: ["google", "github", "email-password"],
            allowDifferentEmails: false, // Only link same email
            allowUnlinkingAll: false, // Prevent unlinking all accounts
        },
    },
});
```

### Trusted Providers

Only trusted providers can auto-link accounts:

```typescript
accountLinking: {
    trustedProviders: ["google", "github"], // Only these can auto-link
},
```

### Link with Different Emails

```typescript
accountLinking: {
    allowDifferentEmails: true, // Allow linking accounts with different emails
},
```

### Manual Account Linking

```typescript
// Link a social account to current user
await authClient.linkSocial({
    provider: "github",
    callbackURL: "/settings",
});

// Unlink an account
await authClient.unlinkAccount({
    providerId: "github",
});
```

---

## Provider-Specific Setup

### Google

1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Create OAuth 2.0 credentials
3. Add authorized redirect URI: `{baseURL}/api/auth/callback/google`

```typescript
google: {
    clientId: process.env.GOOGLE_CLIENT_ID!,
    clientSecret: process.env.GOOGLE_CLIENT_SECRET!,
},
```

### GitHub

1. Go to [GitHub Settings > Developer settings](https://github.com/settings/developers)
2. Create new OAuth App
3. Set callback URL: `{baseURL}/api/auth/callback/github`

```typescript
github: {
    clientId: process.env.GITHUB_CLIENT_ID!,
    clientSecret: process.env.GITHUB_CLIENT_SECRET!,
},
```

### Discord

1. Go to [Discord Developer Portal](https://discord.com/developers/applications)
2. Create new application
3. Add OAuth2 redirect: `{baseURL}/api/auth/callback/discord`

```typescript
discord: {
    clientId: process.env.DISCORD_CLIENT_ID!,
    clientSecret: process.env.DISCORD_CLIENT_SECRET!,
},
```

### Apple

1. Go to [Apple Developer Portal](https://developer.apple.com/)
2. Create App ID and Service ID
3. Configure Sign in with Apple

```typescript
apple: {
    clientId: process.env.APPLE_CLIENT_ID!, // Service ID
    clientSecret: process.env.APPLE_CLIENT_SECRET!, // Generated JWT
},
```

### Microsoft

1. Go to [Azure Portal](https://portal.azure.com/)
2. Register application in Azure AD
3. Add redirect URI: `{baseURL}/api/auth/callback/microsoft`

```typescript
microsoft: {
    clientId: process.env.MICROSOFT_CLIENT_ID!,
    clientSecret: process.env.MICROSOFT_CLIENT_SECRET!,
},
```

### Twitter/X

1. Go to [Twitter Developer Portal](https://developer.twitter.com/)
2. Create project and app
3. Set callback URL: `{baseURL}/api/auth/callback/twitter`

```typescript
twitter: {
    clientId: process.env.TWITTER_CLIENT_ID!,
    clientSecret: process.env.TWITTER_CLIENT_SECRET!,
},
```

---

## Environment Variables

Example `.env` file:

```env
# Google
GOOGLE_CLIENT_ID=your-google-client-id
GOOGLE_CLIENT_SECRET=your-google-client-secret

# GitHub
GITHUB_CLIENT_ID=your-github-client-id
GITHUB_CLIENT_SECRET=your-github-client-secret

# Discord
DISCORD_CLIENT_ID=your-discord-client-id
DISCORD_CLIENT_SECRET=your-discord-client-secret

# Apple
APPLE_CLIENT_ID=your-apple-service-id
APPLE_CLIENT_SECRET=your-apple-jwt-secret

# Microsoft
MICROSOFT_CLIENT_ID=your-microsoft-client-id
MICROSOFT_CLIENT_SECRET=your-microsoft-client-secret

# Twitter
TWITTER_CLIENT_ID=your-twitter-client-id
TWITTER_CLIENT_SECRET=your-twitter-client-secret
```

---

## Complete Example

```typescript
// auth.ts
import { betterAuth } from "better-auth";
import { Pool } from "pg";

export const auth = betterAuth({
    database: new Pool({ connectionString: process.env.DATABASE_URL }),
    
    socialProviders: {
        google: {
            clientId: process.env.GOOGLE_CLIENT_ID!,
            clientSecret: process.env.GOOGLE_CLIENT_SECRET!,
            mapProfileToUser: (profile) => ({
                firstName: profile.given_name,
                lastName: profile.family_name,
            }),
        },
        github: {
            clientId: process.env.GITHUB_CLIENT_ID!,
            clientSecret: process.env.GITHUB_CLIENT_SECRET!,
            scope: ["read:user", "user:email"],
        },
        discord: {
            clientId: process.env.DISCORD_CLIENT_ID!,
            clientSecret: process.env.DISCORD_CLIENT_SECRET!,
        },
    },

    account: {
        accountLinking: {
            enabled: true,
            trustedProviders: ["google", "github", "discord"],
        },
    },
});
```

```tsx
// components/social-login.tsx
"use client";
import { authClient } from "@/lib/auth-client";

export function SocialLogin() {
    const handleSocialLogin = async (provider: string) => {
        await authClient.signIn.social({
            provider,
            callbackURL: "/dashboard",
            errorCallbackURL: "/login?error=auth_failed",
        });
    };

    return (
        <div className="social-buttons">
            <button onClick={() => handleSocialLogin("google")}>
                Continue with Google
            </button>
            <button onClick={() => handleSocialLogin("github")}>
                Continue with GitHub
            </button>
            <button onClick={() => handleSocialLogin("discord")}>
                Continue with Discord
            </button>
        </div>
    );
}
```
