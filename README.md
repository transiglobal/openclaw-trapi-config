# openclaw-trapi-config

OpenClaw 技能（Skill）：配置传米科技自定义 Provider（trapi + linkapi）及模型。

## 简介

本技能帮助用户在任意 OpenClaw 实例上配置传米科技提供的大模型中转 API 服务，包含预置模型和别名，并支持动态添加新模型。

两个 Provider 共享同一个 API Key，统一使用 Anthropic Messages 格式。

## 服务信息

### trapi（国产大模型中转）

| 项目 | 值 |
|------|-----|
| Provider 名称 | `trapi` |
| API 地址 | `https://lapi.transiglobal.com` |
| API 格式 | `anthropic-messages` |
| 运营方 | 传米科技 |

### linkapi（海外模型中转）

| 项目 | 值 |
|------|-----|
| Provider 名称 | `linkapi` |
| API 地址 | `https://hk.linkapi.ai` |
| API 格式 | `anthropic-messages` |
| 运营方 | 传米科技 |

## 预置模型

### trapi 模型

| 模型 ID | 别名 | 输入类型 | 上下文窗口 | 最大输出 |
|---------|------|---------|-----------|---------|
| GLM-5-Turbo | `glm5t` | 文本 | 200K | 64K |
| GLM-5.1 | `glm51` | 文本 | 200K | 64K |
| GLM-4.5-Air | `glm45a` | 文本 | 200K | 64K |
| MiniMax-M2 | `mxm2` | 文本 | 200K | 64K |
| MiniMax-M2.7 | `mxm27` | 文本 | 200K | 64K |
| K2.6-code-preview | `kimi` | 文本 + 图片 | 200K | 64K |
| deepseek-v4-pro | `dsv4p` | 文本 | 200K | 64K |
| deepseek-v4-flash | `dsv4f` | 文本 | 200K | 64K |
| gpt-5.5 | `gpt55` | 文本 | 200K | 64K |

### linkapi 模型

| 模型 ID | 别名 | 输入类型 | 上下文窗口 | 最大输出 |
|---------|------|---------|-----------|---------|
| claude-opus-4-6 | `opus` | 文本 | 300K | 64K |
| [次]claude-opus-4-6 | `copus` | 文本 | 300K | 64K |
| claude-sonnet-4-6 | `sonnet` | 文本 | 300K | 64K |
| [次]claude-sonnet-4-6 | `csonnet` | 文本 | 300K | 64K |
| gemini-3.1-pro-preview | `gemini` | 文本 | 300K | 64K |
| [次]gemini-3.1-pro-preview | `cgemini` | 文本 | 300K | 64K |
| claude-opus-4.7 | `ops47` | 文本 | 300K | 64K |

> `[次]` 前缀表示次级线路（备用节点），与主线路模型相同但走不同中转通道。

## 快捷切换模型

```
/glm45a   → GLM-4.5-Air（轻量）
/glm5t    → GLM-5-Turbo（高性价比）
/glm51    → GLM-5.1（旗舰）
/mxm2     → MiniMax-M2
/mxm27    → MiniMax-M2.7
/kimi     → K2.6-code-preview（支持图片）
/dsv4p    → DeepSeek V4 Pro
/dsv4f    → DeepSeek V4 Flash（快速）
/gpt55    → GPT-5.5
/opus     → Claude Opus 4.6（旗舰）
/sonnet   → Claude Sonnet 4.6（均衡）
/gemini   → Gemini 3.1 Pro
/ops47    → Claude Opus 4.7（最新旗舰）
```

## 使用方式

### 首次配置

在 OpenClaw 对话中说：

> "配置 trapi"

技能会引导你完成：
1. **提供 API Key**（必须提供，不会使用任何默认 key）
2. **自动写入 Provider 配置**到 `openclaw.json`
3. **注册模型别名**（glm5t / glm51 / opus / sonnet 等）
4. **逐个模型验证**，确保连通性
5. **检查已有配置**，只补齐缺失部分，不重复配置

### 添加新模型

当 Provider 已配置好后，说：

> "给 trapi 添加 Qwen-3-Plus"

技能会：
1. 询问模型全称和所属 Provider（trapi 或 linkapi）
2. 自动生成 ≤5 字符的别名（避免与已有别名冲突）
3. 默认 200K 上下文 / 64K 最大输出（linkapi 默认 300K），支持自定义
4. 写入配置并验证

## 安全说明

- **API Key 必须由用户提供**，技能中不存储任何默认密钥
- 未提供 Key 时配置流程会立即终止
- 所有配置通过 OpenClaw 标准的 `gateway config.patch` 写入

## 许可证

MIT
