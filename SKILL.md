---
name: openclaw-trapi-config
description: |
  Configure Transiglobal's "trapi" custom provider and models in OpenClaw. Guides users through adding the provider, required models, and aliases to openclaw.json. Also supports dynamically adding new models to an existing trapi provider. Triggers on phrases like "配置 trapi", "安装 trapi", "添加 trapi provider", "trapi 配置", "Transiglobal API", "配置传米 API", "trapi 添加模型", "trapi add model", or when setting up GLM-5-Turbo / GLM-5.1 / MiniMax-M2.7 / K2.6-code-preview via the trapi provider. Use when a user wants to add the Transiglobal relay API (lapi.transiglobal.com) as a model provider, or add new models to an existing trapi provider.
---

# Transiglobal API (trapi) Provider Configuration

Configure the `trapi` custom provider with Anthropic Messages API format on any OpenClaw instance.

## Provider Info

- **Provider name**: `trapi`
- **Base URL**: `https://lapi.transiglobal.com`
- **API format**: `anthropic-messages`
- **Owner**: 传米科技 (Transiglobal)

## Prerequisites

1. User **must provide their own API key** — never use a default or hardcoded key
2. If user does not provide an API key, **stop immediately** and ask for it; do not proceed without one
3. OpenClaw must be installed and running on the target machine

## Step 1: Ask for API Key

Before any configuration, ask the user for their `trapi` API key. Acceptable formats:
- Direct paste of the key string
- Key from environment variable (if the user prefers)

If the user declines or fails to provide a key, end the workflow with a clear message: *"trapi provider 配置需要 API key，未提供无法继续。请获取 key 后重新运行。"*

## Step 2: Configure Provider

Use `gateway config.patch` to add the `trapi` provider to `models.providers`. The patch targets:

```
models.providers.trapi
```

Patch JSON:

```json
{
  "models": {
    "providers": {
      "trapi": {
        "baseUrl": "https://lapi.transiglobal.com",
        "apiKey": "<USER_PROVIDED_API_KEY>",
        "api": "anthropic-messages",
        "models": [
          {
            "id": "GLM-5-Turbo",
            "name": "GLM-5-Turbo (Transiglobal)",
            "api": "anthropic-messages",
            "reasoning": true,
            "input": ["text"],
            "cost": { "input": 0, "output": 0, "cacheRead": 0, "cacheWrite": 0 },
            "contextWindow": 200000,
            "maxTokens": 64000
          },
          {
            "id": "GLM-5.1",
            "name": "GLM-5.1 (Transiglobal)",
            "api": "anthropic-messages",
            "reasoning": true,
            "input": ["text"],
            "cost": { "input": 0, "output": 0, "cacheRead": 0, "cacheWrite": 0 },
            "contextWindow": 200000,
            "maxTokens": 64000
          },
          {
            "id": "MiniMax-M2.7",
            "name": "MiniMax-M2.7 (Transiglobal)",
            "api": "anthropic-messages",
            "reasoning": true,
            "input": ["text"],
            "cost": { "input": 0, "output": 0, "cacheRead": 0, "cacheWrite": 0 },
            "contextWindow": 200000,
            "maxTokens": 64000
          },
          {
            "id": "K2.6-code-preview",
            "name": "K2.6 Code Preview (Transiglobal)",
            "api": "anthropic-messages",
            "reasoning": true,
            "input": ["text", "image"],
            "cost": { "input": 0, "output": 0, "cacheRead": 0, "cacheWrite": 0 },
            "contextWindow": 200000,
            "maxTokens": 64000
          }
        ]
      }
    }
  }
}
```

## Step 3: Configure Model Aliases

Use `gateway config.patch` to add aliases under `agents.defaults.models`:

```json
{
  "agents": {
    "defaults": {
      "models": {
        "trapi/GLM-5-Turbo": { "alias": "glm5t" },
        "trapi/GLM-5.1": { "alias": "glm51" },
        "trapi/MiniMax-M2.7": { "alias": "mxm27" },
        "trapi/K2.6-code-preview": { "alias": "kimi" }
      }
    }
  }
}
```

## Step 4: Apply and Restart

Use `gateway config.patch` with `note` parameter to describe what was done. The gateway will hot-reload or restart automatically.

If patch is not available, fallback to `gateway config.apply` with the full config.

## Step 5: Validate Each Model

After configuration is applied, validate all 4 models using subagent calls. Spawn a subagent for each model with a simple test prompt:

```
sessions_spawn(
  model: "trapi/<MODEL_ID>",
  task: "Reply with exactly: OK",
  runtime: "subagent"
)
```

Validate in order:
1. `trapi/GLM-5-Turbo` (alias: glm5t)
2. `trapi/GLM-5.1` (alias: glm51)
3. `trapi/MiniMax-M2.7` (alias: mxm27)
4. `trapi/K2.6-code-preview` (alias: kimi)

### Validation Criteria

For each model, the validation passes if:
- The subagent returns a response (not an error/timeout)
- The response contains text output

### Validation Report

After all validations, provide a summary:

| Model | Alias | Status |
|-------|-------|--------|
| GLM-5-Turbo | glm5t | ✅/❌ |
| GLM-5.1 | glm51 | ✅/❌ |
| MiniMax-M2.7 | mxm27 | ✅/❌ |
| K2.6-code-preview | kimi | ✅/❌ |

## Add New Model (trapi already configured)

Use this flow when `trapi` provider already exists and user wants to add one or more new models.

### Required from User

| Field | Required | Default |
|-------|----------|---------|
| Model ID (full name) | ✅ Yes | — |
| contextWindow | No | 200000 (200K) |
| maxTokens | No | 64000 (64K) |
| input types | No | `["text"]` (can add `"image"`) |

### Alias Generation Rules

Generate alias **≤ 5 characters**, following these priorities:

1. **Known patterns** — derive from common abbreviations:
   - `GLM-` → `glm`, `MiniMax-` → `mxm`, `K2.6` → `kimi`, `mimo` → `mimo`
2. **Remove separators** — strip `-`, `.`, spaces: `GLM-5-Turbo` → `GLM5Turbo`
3. **Take first 5 chars** of the cleaned string: `GLM5T`
4. **Lowercase** the result: `glm5t`
5. **If conflict** with existing alias, append a digit suffix: `glm5t2`

### Existing Aliases (must not duplicate)

| Model | Alias |
|-------|-------|
| GLM-5-Turbo | glm5t |
| GLM-5.1 | glm51 |
| MiniMax-M2.7 | mxm27 |
| K2.6-code-preview | kimi |

### Flow

1. Ask user for **model ID** (full name)
2. Ask if they want to customize contextWindow, maxTokens, or input types
3. Generate alias using rules above
4. Show user the proposed config and ask for confirmation
5. Use `gateway config.patch` to add model to `models.providers.trapi.models` array
6. Use `gateway config.patch` to add alias to `agents.defaults.models`
7. Validate with subagent: `sessions_spawn(model: "trapi/<MODEL_ID>", task: "Reply with exactly: OK", runtime: "subagent")`
8. Report result

### Example: Adding "Qwen-3-Plus"

User says: "给 trapi 加一个 Qwen-3-Plus"

1. Model ID: `Qwen-3-Plus`
2. Defaults: contextWindow 200K, maxTokens 64K, input `["text"]`
3. Alias: clean `Qwen3Plus` → `qwen3` (no conflict) → alias: `qwen3`
4. Patch model into `trapi.models`, alias into `agents.defaults.models`
5. Validate

## Troubleshooting

- **401/403**: API key invalid or expired — ask user to verify
- **Connection timeout**: Check network access to `lapi.transiglobal.com`
- **Model not found**: The model ID may have changed — check with Transiglobal
- **config.patch conflict**: If `trapi` provider already exists, patch will merge; if aliases conflict, warn user
- **Alias conflict**: Regenerate with numeric suffix and confirm with user
