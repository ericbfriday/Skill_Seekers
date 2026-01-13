# Better Auth Two-Factor Authentication (2FA)

## Table of Contents

1. [Installation](#installation)
2. [Enabling 2FA](#enabling-2fa)
3. [Sign In with 2FA](#sign-in-with-2fa)
4. [Disabling 2FA](#disabling-2fa)
5. [TOTP (Time-Based OTP)](#totp-time-based-otp)
6. [OTP (One-Time Password)](#otp-one-time-password)
7. [Backup Codes](#backup-codes)
8. [Trusted Devices](#trusted-devices)
9. [Configuration Options](#configuration-options)
10. [Schema](#schema)

---

## Installation

### 1. Add Server Plugin

```typescript
// auth.ts
import { betterAuth } from "better-auth";
import { twoFactor } from "better-auth/plugins";

export const auth = betterAuth({
    appName: "My App", // Used as TOTP issuer
    plugins: [
        twoFactor(),
    ],
});
```

### 2. Migrate Database

```bash
# Generate schema
npx @better-auth/cli generate

# Or migrate directly
npx @better-auth/cli migrate
```

### 3. Add Client Plugin

```typescript
// auth-client.ts
import { createAuthClient } from "better-auth/client";
import { twoFactorClient } from "better-auth/client/plugins";

export const authClient = createAuthClient({
    plugins: [
        twoFactorClient(),
    ],
});
```

---

## Enabling 2FA

```typescript
// Client
const { data, error } = await authClient.twoFactor.enable({
    password: "user-password", // Required
    issuer: "my-app-name", // Optional, defaults to appName
});
// Returns: { totpURI, backupCodes }

// Server
const data = await auth.api.enableTwoFactor({
    body: {
        password: "user-password",
        issuer: "my-app-name",
    },
    headers: await headers(),
});
```

**Important**: `twoFactorEnabled` won't be `true` until the user verifies their TOTP code.

> **Note**: 2FA can only be enabled for credential accounts (email/password). Social accounts are assumed to have 2FA from the provider.

---

## Sign In with 2FA

When a user with 2FA enabled signs in, the response includes `twoFactorRedirect: true`.

### Handle in Callback

```typescript
await authClient.signIn.email({
    email: "user@example.com",
    password: "password123",
}, {
    onSuccess(context) {
        if (context.data.twoFactorRedirect) {
            // Redirect to 2FA verification page
            router.push("/verify-2fa");
        }
    },
});
```

### Handle Globally

```typescript
const authClient = createAuthClient({
    plugins: [
        twoFactorClient({
            onTwoFactorRedirect() {
                window.location.href = "/verify-2fa";
            },
        }),
    ],
});
```

### Server-Side Check

```typescript
const response = await auth.api.signInEmail({
    body: { email: "test@test.com", password: "test" },
});

if ("twoFactorRedirect" in response) {
    // Handle 2FA verification
}
```

---

## Disabling 2FA

```typescript
// Client
const { data, error } = await authClient.twoFactor.disable({
    password: "user-password",
});

// Server
const data = await auth.api.disableTwoFactor({
    body: { password: "user-password" },
    headers: await headers(),
});
```

---

## TOTP (Time-Based OTP)

TOTP generates codes offline using an authenticator app. Codes are valid for 30 seconds (configurable).

### Getting TOTP URI (for QR Code)

```typescript
// Client
const { data, error } = await authClient.twoFactor.getTotpUri({
    password: "user-password",
});

// Server
const data = await auth.api.getTOTPURI({
    body: { password: "user-password" },
    headers: await headers(),
});
```

### Display QR Code (React Example)

```tsx
import QRCode from "react-qr-code";
import { useQuery } from "@tanstack/react-query";

export function TwoFactorSetup({ password }: { password: string }) {
    const { data: session } = authClient.useSession();
    
    const { data: qr } = useQuery({
        queryKey: ["two-factor-qr"],
        queryFn: async () => {
            const res = await authClient.twoFactor.getTotpUri({ password });
            return res.data;
        },
        enabled: !!session?.user.twoFactorEnabled,
    });

    return <QRCode value={qr?.totpURI || ""} />;
}
```

### Verifying TOTP

```typescript
// Client
const { data, error } = await authClient.twoFactor.verifyTotp({
    code: "123456",
    trustDevice: true, // Remember device for 30 days
});

// Server
const data = await auth.api.verifyTOTP({
    body: {
        code: "123456",
        trustDevice: true,
    },
});
```

Better Auth accepts TOTP codes from one period before and after the current time for tolerance.

---

## OTP (One-Time Password)

OTP sends a random code to the user's email or phone.

### Configure sendOTP

```typescript
import { betterAuth } from "better-auth";
import { twoFactor } from "better-auth/plugins";

export const auth = betterAuth({
    plugins: [
        twoFactor({
            otpOptions: {
                async sendOTP({ user, otp }, ctx) {
                    // Send OTP via email or SMS
                    await sendEmail(user.email, {
                        subject: "Your verification code",
                        body: `Your code is: ${otp}`,
                    });
                },
            },
        }),
    ],
});
```

### Sending OTP

```typescript
// Client
const { data, error } = await authClient.twoFactor.sendOtp({
    trustDevice: true, // Optional
});

// Server
const data = await auth.api.send2FaOTP({
    body: { trustDevice: true },
});
```

### Verifying OTP

```typescript
// Client
const { data, error } = await authClient.twoFactor.verifyOtp({
    code: "123456",
    trustDevice: true,
});

// Server
const data = await auth.api.verifyOTP({
    body: {
        code: "123456",
        trustDevice: true,
    },
});
```

---

## Backup Codes

Backup codes provide account recovery if the user loses access to their authenticator.

### Generate Backup Codes

```typescript
// Client
const { data, error } = await authClient.twoFactor.generateBackupCodes({
    password: "user-password",
});

// Server
const data = await auth.api.generateBackupCodes({
    body: { password: "user-password" },
    headers: await headers(),
});
```

> **Warning**: Generating new backup codes deletes all existing codes.

### Using Backup Codes

```typescript
// Client
const { data, error } = await authClient.twoFactor.verifyBackupCode({
    code: "123456",
    trustDevice: true,
    disableSession: false, // Set true to prevent setting session cookie
});

// Server
const data = await auth.api.verifyBackupCode({
    body: {
        code: "123456",
        disableSession: false,
        trustDevice: true,
    },
});
```

> **Note**: Each backup code can only be used once.

### Viewing Backup Codes

Only allowed with a fresh session (recently created):

```typescript
// Client
const { data, error } = await authClient.twoFactor.viewBackupCodes({
    userId: "user-id", // Optional
});

// Server
const data = await auth.api.viewBackupCodes({
    body: { userId: "user-id" },
});
```

---

## Trusted Devices

Mark devices as trusted to skip 2FA for 30 days (refreshed on each sign-in).

```typescript
await authClient.twoFactor.verifyTotp({
    code: "123456",
    trustDevice: true, // Device trusted for 30 days
});
```

---

## Configuration Options

### Server Options

```typescript
twoFactor({
    // Table name for 2FA data
    twoFactorTable: "twoFactor", // default

    // Skip verification on enable
    skipVerificationOnEnable: false, // default

    // TOTP issuer name (shown in authenticator apps)
    issuer: "My App",

    // TOTP options
    totpOptions: {
        digits: 6, // OTP length (default: 6)
        period: 30, // Seconds per code (default: 30)
    },

    // OTP options
    otpOptions: {
        sendOTP: async ({ user, otp }, ctx) => {
            // Send OTP implementation
        },
        period: 3, // Minutes until expiry (default: 3)
        storeOTP: "plain", // "plain", "encrypted", or "hashed"
    },

    // Backup code options
    backupCodeOptions: {
        amount: 10, // Number of codes (default: 10)
        length: 10, // Code length (default: 10)
        customBackupCodesGenerate: () => {
            // Custom generator returning string[]
        },
        storeBackupCodes: "plain", // "plain" or "encrypted"
    },
});
```

### Client Options

```typescript
twoFactorClient({
    onTwoFactorRedirect() {
        // Called when user needs to verify 2FA
        window.location.href = "/2fa";
    },
});
```

---

## Schema

The plugin adds:

### User Table Fields

| Field | Type | Description |
|-------|------|-------------|
| twoFactorEnabled | boolean (optional) | Whether 2FA is enabled |

### TwoFactor Table

| Field | Type | Description |
|-------|------|-------------|
| id | string | Primary key |
| userId | string | Foreign key to user |
| secret | string (optional) | TOTP secret |
| backupCodes | string (optional) | Encrypted backup codes |

---

## Complete Example

### Auth Configuration

```typescript
// auth.ts
import { betterAuth } from "better-auth";
import { twoFactor } from "better-auth/plugins";

export const auth = betterAuth({
    appName: "My App",
    database: new Pool({ connectionString: process.env.DATABASE_URL }),
    emailAndPassword: { enabled: true },
    plugins: [
        twoFactor({
            otpOptions: {
                async sendOTP({ user, otp }) {
                    await sendEmail(user.email, {
                        subject: "Verification Code",
                        body: `Your code: ${otp}`,
                    });
                },
            },
        }),
    ],
});
```

### Client Configuration

```typescript
// auth-client.ts
import { createAuthClient } from "better-auth/react";
import { twoFactorClient } from "better-auth/client/plugins";

export const authClient = createAuthClient({
    plugins: [
        twoFactorClient({
            onTwoFactorRedirect() {
                window.location.href = "/verify-2fa";
            },
        }),
    ],
});
```

### 2FA Setup Component

```tsx
import { useState } from "react";
import QRCode from "react-qr-code";
import { authClient } from "@/lib/auth-client";

export function TwoFactorSetup() {
    const [password, setPassword] = useState("");
    const [totpUri, setTotpUri] = useState("");
    const [backupCodes, setBackupCodes] = useState<string[]>([]);
    const [verifyCode, setVerifyCode] = useState("");

    const handleEnable = async () => {
        const { data, error } = await authClient.twoFactor.enable({ password });
        if (data) {
            setTotpUri(data.totpURI);
            setBackupCodes(data.backupCodes);
        }
    };

    const handleVerify = async () => {
        const { data, error } = await authClient.twoFactor.verifyTotp({
            code: verifyCode,
        });
        if (data) {
            alert("2FA enabled successfully!");
        }
    };

    return (
        <div>
            {!totpUri ? (
                <div>
                    <input
                        type="password"
                        value={password}
                        onChange={(e) => setPassword(e.target.value)}
                        placeholder="Enter password"
                    />
                    <button onClick={handleEnable}>Enable 2FA</button>
                </div>
            ) : (
                <div>
                    <h3>Scan QR Code</h3>
                    <QRCode value={totpUri} />
                    
                    <h3>Backup Codes (save these!)</h3>
                    <ul>
                        {backupCodes.map((code) => (
                            <li key={code}>{code}</li>
                        ))}
                    </ul>

                    <input
                        type="text"
                        value={verifyCode}
                        onChange={(e) => setVerifyCode(e.target.value)}
                        placeholder="Enter code from app"
                    />
                    <button onClick={handleVerify}>Verify</button>
                </div>
            )}
        </div>
    );
}
```

### 2FA Verification Page

```tsx
import { useState } from "react";
import { authClient } from "@/lib/auth-client";
import { useRouter } from "next/navigation";

export function TwoFactorVerify() {
    const router = useRouter();
    const [code, setCode] = useState("");
    const [trustDevice, setTrustDevice] = useState(false);
    const [useBackup, setUseBackup] = useState(false);

    const handleVerify = async () => {
        const method = useBackup
            ? authClient.twoFactor.verifyBackupCode
            : authClient.twoFactor.verifyTotp;

        const { data, error } = await method({
            code,
            trustDevice,
        });

        if (data) {
            router.push("/dashboard");
        }
    };

    return (
        <div>
            <h2>Two-Factor Authentication</h2>
            <input
                type="text"
                value={code}
                onChange={(e) => setCode(e.target.value)}
                placeholder={useBackup ? "Backup code" : "6-digit code"}
            />
            <label>
                <input
                    type="checkbox"
                    checked={trustDevice}
                    onChange={(e) => setTrustDevice(e.target.checked)}
                />
                Trust this device
            </label>
            <button onClick={handleVerify}>Verify</button>
            <button onClick={() => setUseBackup(!useBackup)}>
                {useBackup ? "Use authenticator" : "Use backup code"}
            </button>
        </div>
    );
}
```
