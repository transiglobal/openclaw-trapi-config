---
name: openclaw-trapi-config
description: |
  配置传米科技 trapi 自定义 Provider 及模型。引导用户将 trapi Provider（lapi.transiglobal.com）添加到 OpenClaw，支持首次安装和动态添加新模型。触发词："配置 trapi"、"安装 trapi"、"添加 trapi provider"、"trapi 配置"、"Transiglobal API"、"配置传米 API"、"trapi 添加模型"、"trapi add model"，或涉及 GLM-5-Turbo / GLM-5.1 / MiniMax-M2.7 / K2.6-code-preview 的 trapi 配置。也可在 trapi 已存在时用于添加新模型。
---

# trapi Provider 配置指南

在任意 OpenClaw 实例上配置 `trapi` 自定义 Provider，使用 Anthropic Messages API 格式。

## 服务信息

- **Provider 名称**：`trapi`
- **Base URL**：`https://lapi.transiglobal.com`
- **API 格式**：`anthropic-messages`
- **运营方**：传米科技（Transiglobal）

## 前置条件

1. 用户**必须提供自己的 API Key** —— 绝不使用默认或硬编码的密钥
2. 如用户未提供 API Key，**立即停止**并要求提供；不可继续执行
3. OpenClaw 必须已安装并运行

## 步骤 1：要求 API Key

进行任何配置前，先向用户索要 `trapi` API Key。接受形式：
- 直接粘贴 Key 字符串
- 通过环境变量引用

如用户拒绝或无法提供，以明确信息终止流程：*"trapi provider 配置需要 API Key，未提供无法继续。请获取 Key 后重新运行。"*

## 步骤 2：配置 Provider

使用 `gateway config.patch` 将 `trapi` provider 添加到 `models.providers`。

```json
{
  "models": {
    "providers": {
      "trapi": {
        "baseUrl": "https://lapi.transiglobal.com",
        "apiKey": "<用户提供的API_KEY>",
        "api": "anthropic-messages",
        "models": [
          {
            "id": "GLM-5-Turbo",
            "name": "GLM-5-Turbo (Transiglobal)",
            "api": "anthropic-messages",
            "reasoning": false,
            "input": ["text"],
            "cost": { "input": 0, "output": 0, "cacheRead": 0, "cacheWrite": 0 },
            "contextWindow": 200000,
            "maxTokens": 64000
          },
          {
            "id": "GLM-5.1",
            "name": "GLM-5.1 (Transiglobal)",
            "api": "anthropic-messages",
            "reasoning": false,
            "input": ["text"],
            "cost": { "input": 0, "output": 0, "cacheRead": 0, "cacheWrite": 0 },
            "contextWindow": 200000,
            "maxTokens": 64000
          },
          {
            "id": "MiniMax-M2.7",
            "name": "MiniMax-M2.7 (Transiglobal)",
            "api": "anthropic-messages",
            "reasoning": false,
            "input": ["text"],
            "cost": { "input": 0, "output": 0, "cacheRead": 0, "cacheWrite": 0 },
            "contextWindow": 200000,
            "maxTokens": 64000
          },
          {
            "id": "K2.6-code-preview",
            "name": "K2.6 Code Preview (Transiglobal)",
            "api": "anthropic-messages",
            "reasoning": false,
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

## 步骤 3：配置模型别名

使用 `gateway config.patch` 添加别名到 `agents.defaults.models`：

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

## 步骤 4：应用配置

使用 `gateway config.patch` 并附带 `note` 参数说明变更内容。Gateway 会自动热加载或重启。

## 步骤 5：逐个验证模型

配置生效后，使用 subagent 逐个验证 4 个模型：

```
sessions_spawn(
  model: "trapi/<模型ID>",
  task: "Reply with exactly: OK",
  runtime: "subagent"
)
```

验证顺序：
1. `trapi/GLM-5-Turbo`（别名：glm5t）
2. `trapi/GLM-5.1`（别名：glm51）
3. `trapi/MiniMax-M2.7`（别名：mxm27）
4. `trapi/K2.6-code-preview`（别名：kimi）

### 验证标准

每个模型验证通过的条件：
- subagent 返回正常响应（非错误/超时）
- 响应包含文本输出

### 验证报告

所有模型验证完成后，输出汇总表：

| 模型 | 别名 | 状态 |
|------|------|------|
| GLM-5-Turbo | glm5t | ✅/❌ |
| GLM-5.1 | glm51 | ✅/❌ |
| MiniMax-M2.7 | mxm27 | ✅/❌ |
| K2.6-code-preview | kimi | ✅/❌ |

## 步骤 6：使用指引

配置完成并通过验证后，向用户展示快捷切换模型的使用方式：

```
/glm5t    → 切换到 GLM-5-Turbo（高性价比）
/glm51    → 切换到 GLM-5.1（旗舰）
/mxm27    → 切换到 MiniMax-M2.7
/kimi     → 切换到 K2.6-code-preview（支持图片）
```

提示用户在对话中直接输入 `/alias` 即可快速切换模型。

## 添加新模型（trapi 已配置时）

当 trapi Provider 已存在，用户需要添加新模型时使用此流程。

### 用户需提供的信息

| 字段 | 是否必填 | 默认值 |
|------|---------|--------|
| 模型 ID（全称） | ✅ 必填 | — |
| contextWindow | 选填 | 200000（200K） |
| maxTokens | 选填 | 64000（64K） |
| input 类型 | 选填 | `["text"]`（可加 `"image"`） |

### 别名生成规则

生成 **≤ 5 字符**的别名，按以下优先级：

1. **已知模式** — 从常见缩写推导：
   - `GLM-` → `glm`，`MiniMax-` → `mxm`，`K2.6` → `kimi`，`mimo` → `mimo`
2. **去除分隔符** — 去掉 `-`、`.`、空格：`GLM-5-Turbo` → `GLM5Turbo`
3. **取前 5 字符**：`GLM5T`
4. **转小写**：`glm5t`
5. **冲突处理**：如与已有别名冲突，追加数字后缀：`glm5t2`

### 已有别名（不可重复）

| 模型 | 别名 |
|------|------|
| GLM-5-Turbo | glm5t |
| GLM-5.1 | glm51 |
| MiniMax-M2.7 | mxm27 |
| K2.6-code-preview | kimi |

### 操作流程

1. 向用户询问**模型 ID**（全称）
2. 询问是否需要自定义 contextWindow、maxTokens 或 input 类型
3. 按上述规则生成别名
4. 向用户展示拟配置方案并确认
5. `gateway config.patch` 添加模型到 `models.providers.trapi.models`
6. `gateway config.patch` 添加别名到 `agents.defaults.models`
7. subagent 验证：`sessions_spawn(model: "trapi/<模型ID>", task: "Reply with exactly: OK", runtime: "subagent")`
8. 报告结果

### 示例：添加 "Qwen-3-Plus"

用户说："给 trapi 加一个 Qwen-3-Plus"

1. 模型 ID：`Qwen-3-Plus`
2. 默认参数：contextWindow 200K，maxTokens 64K，input `["text"]`
3. 别名：清理为 `Qwen3Plus` → `qwen3`（无冲突）→ 别名：`qwen3`
4. Patch 模型到 `trapi.models`，别名到 `agents.defaults.models`
5. 验证

## 故障排查

- **401/403**：API Key 无效或过期 — 请用户核实
- **连接超时**：检查到 `lapi.transiglobal.com` 的网络连通性
- **Model not found**：模型 ID 可能已变更 — 联系传米科技确认
- **config.patch 冲突**：trapi provider 已存在时 patch 会合并；别名冲突时警告用户
- **别名冲突**：重新生成带数字后缀的别名并与用户确认
