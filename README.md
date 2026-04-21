# openclaw-trapi-config

OpenClaw 技能（Skill）：配置传米科技 trapi 自定义 Provider 及模型。

## 简介

trapi 是传米科技（Transiglobal）提供的大模型中转 API 服务，统一使用 Anthropic Messages 格式。本技能帮助用户在任意 OpenClaw 实例上一键配置 trapi Provider，包含预置模型和别名，并支持动态添加新模型。

## 服务信息

| 项目 | 值 |
|------|-----|
| Provider 名称 | `trapi` |
| API 地址 | `https://lapi.transiglobal.com` |
| API 格式 | `anthropic-messages` |
| 运营方 | 传米科技 |

## 预置模型

| 模型 ID | 别名 | 输入类型 | 上下文窗口 | 最大输出 |
|---------|------|---------|-----------|---------|
| GLM-5-Turbo | `glm5t` | 文本 | 200K | 64K |
| GLM-5.1 | `glm51` | 文本 | 200K | 64K |
| MiniMax-M2.7 | `mxm27` | 文本 | 200K | 64K |
| K2.6-code-preview | `kimi` | 文本 + 图片 | 200K | 64K |

### 快捷切换模型

## 使用方式

### 安装技能

将 `openclaw-trapi-config.skill` 文件放到 OpenClaw 的 skills 目录下，或通过以下命令安装：

```bash
openclaw skill install openclaw-trapi-config.skill
```

### 首次配置 trapi

在 OpenClaw 对话中说：

> "配置 trapi"

技能会引导你完成：
1. **提供 API Key**（必须提供，不会使用任何默认 key）
2. **自动写入 Provider 配置**到 `openclaw.json`
3. **注册模型别名**（glm5t / glm51 / mxm27 / kimi）
4. **逐个模型验证**，确保连通性

### 添加新模型

当 trapi 已配置好后，说：

> "给 trapi 添加 Qwen-3-Plus"

技能会：
1. 询问模型全称
2. 自动生成 ≤5 字符的别名（避免与已有别名冲突）
3. 默认 200K 上下文 / 64K 最大输出，支持自定义
4. 写入配置并验证

## 安全说明

- **API Key 必须由用户提供**，技能中不存储任何默认密钥
- 未提供 Key 时配置流程会立即终止
- 所有配置通过 OpenClaw 标准的 `gateway config.patch` 写入

## 许可证

MIT
