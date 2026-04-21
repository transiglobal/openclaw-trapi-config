# Custom Provider 配置参考

> 来源：OpenClaw 官方文档
> - https://docs.openclaw.ai/concepts/models
> - https://docs.openclaw.ai/concepts/model-providers

## Custom Provider 配置方式

在 `openclaw.json` 的 `models.providers` 中添加自定义 provider：

```json
{
  "models": {
    "providers": {
      "<provider_id>": {
        "baseUrl": "https://api.example.com",
        "apiKey": "sk-xxx",
        "api": "anthropic-messages",
        "models": [
          {
            "id": "model-name",
            "name": "Display Name",
            "api": "anthropic-messages",
            "reasoning": false,
            "input": ["text"],
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

## API 格式（api 字段）

| 值 | 说明 |
|----|------|
| `anthropic-messages` | Anthropic Messages API 格式 |
| `openai-completions` | OpenAI Chat Completions 格式 |
| `openai-responses` | OpenAI Responses API 格式 |

## 模型字段说明

| 字段 | 类型 | 说明 |
|------|------|------|
| `id` | string | 模型 ID（必填），用于 provider/model 引用 |
| `name` | string | 显示名称 |
| `api` | string | API 格式，可覆盖 provider 级别设置 |
| `reasoning` | boolean | 是否支持推理/思考模式 |
| `input` | string[] | 支持的输入类型：`"text"`, `"image"` |
| `cost` | object | 费用设置，免费模型全部设为 0 |
| `contextWindow` | number | 上下文窗口大小（token 数） |
| `maxTokens` | number | 最大输出 token 数 |

## 模型别名配置

在 `agents.defaults.models` 中注册别名：

```json
{
  "agents": {
    "defaults": {
      "models": {
        "trapi/GLM-5-Turbo": { "alias": "glm5t" },
        "trapi/GLM-5.1": { "alias": "glm51" }
      }
    }
  }
}
```

别名注册后可通过 `/alias` 快速切换模型。

## 注意事项

- `agents.defaults.models` 设置后成为 **allowlist**，只有列表中的模型才可用
- Provider 配置会写入 `models.json`，合并模式（merge）下不会覆盖已有配置
- API Key 会从 `models.providers` 和 auth profiles 两处解析
- 模型引用格式：`provider/model`（如 `trapi/GLM-5-Turbo`）

## 相关 CLI 命令

```bash
openclaw models list           # 查看已配置模型
openclaw models status         # 查看当前模型和认证状态
openclaw models aliases list   # 查看所有别名
openclaw models aliases add <alias> <provider/model>  # 添加别名
```
