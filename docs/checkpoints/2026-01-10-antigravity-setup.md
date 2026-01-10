# Session Checkpoint: Antigravity Plugin Installation & Configuration
**Date:** January 10, 2026
**Status:** Completed & Verified

## Session Overview
This session focused on the integration of the `opencode-antigravity-auth@beta` plugin into the OpenCode environment. The primary goal was to establish a robust authentication flow and configure high-performance model variants for both Claude and Gemini architectures.

### Achievements:
- Successfully installed the `opencode-antigravity-auth@beta` plugin.
- Configured the global `~/.config/opencode/opencode.json` with Antigravity and Gemini 3 model definitions.
- Established Google OAuth as the primary authentication provider.
- Verified end-to-end functionality via model testing and quota confirmation.

---

## Technical Discoveries

### 1. Interactive Authentication Flow
The plugin introduces a strictly interactive authentication process. Unlike standard API key configurations, Antigravity requires:
- Execution of `opencode auth login`.
- Explicit selection of the `google` provider.
- Completion of the OAuth consent flow in the browser.

### 2. Advanced Model Variant System
A key discovery was the new tiered variant system which allows for granular control over model performance and latency:
- **Claude Variants:** Standardized on `low` and `max`.
- **Gemini 3 Variants:** Expanded to a four-tier system: `minimal`, `low`, `medium`, and `high`.

### 3. Dual Quota Architecture
The environment now operates under a split quota system:
- **Antigravity Quota:** Applies to all models prefixed with `antigravity-` (e.g., `antigravity-claude-3.5-sonnet`).
- **Gemini CLI Quota:** Applies to preview and experimental models (e.g., `gemini-2.5-flash-preview-04-17`).

---

## Key Configuration Details

### Provider Specification
The configuration utilizes the `@ai-sdk/google` provider for seamless integration with the Antigravity backend.

### Model Definitions (`opencode.json`)
The following configuration syntax was implemented in `~/.config/opencode/opencode.json`:

```json
{
  "providers": {
    "google": {
      "package": "@ai-sdk/google",
      "config": {
        "authType": "oauth"
      }
    }
  },
  "models": {
    "antigravity-claude-3.5-sonnet": {
      "variants": ["low", "max"],
      "provider": "google"
    },
    "antigravity-claude-3.5-haiku": {
      "variants": ["low", "max"],
      "provider": "google"
    },
    "gemini-2.5-flash-preview-04-17": {
      "variants": ["minimal", "low", "medium", "high"],
      "provider": "google"
    }
  }
}
```

---

## Command Sequences & Execution

### Installation
```bash
npm install -g opencode-antigravity-auth@beta
```

### Authentication
```bash
# Trigger interactive Google OAuth
opencode auth login --provider google
```

### Model Management
```bash
# List available models and verify installation
opencode models list

# Check current quota status
opencode quota status
```

---

## Verification Steps

1. **Model Presence**: Verified that `antigravity-claude-3.5-sonnet` and `gemini-2.5-flash-preview-04-17` appear in the `opencode models list` output.
2. **Functional Testing**:
   - Tested **Regular Models**: Successful response from `antigravity-claude-3.5-sonnet:low`.
   - Tested **Thinking Models**: Verified reasoning capabilities of `max` variants.
3. **Quota Confirmation**: Confirmed that requests are correctly billed against the Antigravity quota vs. the Gemini CLI quota depending on the model prefix.

---
**Note:** This checkpoint preserves the state of the Antigravity integration as of Jan 10, 2026. Future updates to the beta plugin should reference these variant definitions for consistency.
