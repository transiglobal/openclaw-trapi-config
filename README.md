# openclaw-trapi-config

用于配置和维护传米科技 trapi 自定义 Provider 的 OpenClaw Skill，支持首次安装、模型增量更新、别名、图像模型、静态验证和故障排查。

## 服务信息

| 项目 | 值 |
|---|---|
| Provider ID | `trapi` |
| Base URL | `https://lapi.transiglobal.com` |
| API Key 环境变量 | `TRAPI_API_KEY` |
| 默认 API 适配器 | `anthropic-messages` |
| GPT 模型适配器 | `openai-responses` |

trapi 支持模型级 API 适配器，不要求所有模型统一使用 Anthropic Messages。

## 预置模型

| 模型 ID | 别名 | API | 输入 | 上下文 | 最大输出 |
|---|---|---|---|---:|---:|
| GLM-5-Turbo | `glm5t` | anthropic-messages | 文本 | 200K | 64K |
| GLM-5.2 | `glm52` | anthropic-messages | 文本 | 200K | 64K |
| GLM-4.5-Air | `glm45a` | anthropic-messages | 文本 | 200K | 64K |
| MiniMax-M2 | `mxm2` | anthropic-messages | 文本 | 200K | 64K |
| MiniMax-M2.7 | `mxm27` | anthropic-messages | 文本 | 200K | 64K |
| MiniMax-M3 | `mxm3` | anthropic-messages | 文本、图片 | 1M | 131K |
| kimi-for-coding | `kimi` | anthropic-messages | 文本、图片 | 262K | 32K |
| GLM-5V-Turbo | `glm5v` | anthropic-messages | 文本、图片 | 200K | 64K |
| PaddleOCR-VL-1.5 | `pocr` | anthropic-messages | 文本、图片 | 131K | 8K |
| deepseek-v4-pro | `dsv4p` | anthropic-messages | 文本 | 1M | 384K |
| deepseek-v4-flash | `dsv4f` | anthropic-messages | 文本 | 1M | 384K |
| gpt-5.5 | `gpt55` | openai-responses | 文本 | 200K | 65K |
| gpt-5.6-sol | `g56s` | openai-responses | 文本、图片 | 372K | 128K |
| gpt-5.6-terra | `g56t` | openai-responses | 文本、图片 | 372K | 128K |
| gpt-5.6-luna | `g56l` | openai-responses | 文本、图片 | 372K | 128K |
| mimo-v2.5-pro | `mimo25p` | anthropic-messages | 文本 | 200K | 64K |
| mimo-v2.5 | `mimo25` | anthropic-messages | 文本、图片、视频、音频 | 200K | 64K |
| claude-opus-4.7 | `opus47` | anthropic-messages | 文本 | 300K | 64K |
| qwen3.7-max | `qwn37` | anthropic-messages | 文本 | 1M | 100K |
| qwen3.6-plus | `qwn36` | anthropic-messages | 文本、图片 | 1M | 100K |

模型目录不设置 `reasoning` 字段，费用元数据暂时全部保持为 0。

## 使用方式

在 OpenClaw 对话中说：

> 配置 trapi

Skill 会执行以下流程：

1. 检查 OpenClaw 版本、Schema 和现有 trapi 配置。
2. 对照预置目录生成新增、更新、保留和额外模型差异。
3. 保留已有认证；首次配置时优先使用 `TRAPI_API_KEY` SecretRef。
4. 展示变更方案，获得确认后使用 CLI `--merge` 安全增量写入。
5. 注册缺失别名，并按需配置 imageModel。
6. 执行配置、模型、别名和 Gateway 静态验证。
7. 经用户确认后，只对本次变更模型执行最小实时调用。

不会使用不完整的 `gateway config.patch` 覆盖模型数组，也不会自动删除目录外模型、覆盖默认模型或重写已有 imageModel。

## 切换模型

在对话中使用 `/model <alias>`：

```text
/model glm5t
/model mxm3
/model g56s
/model g56t
/model g56l
```

不能直接使用 `/<alias>` 切换模型。

## 图像模型

建议链仅引用已配置且认证可用的 trapi 模型：

1. `trapi/kimi-for-coding`
2. `trapi/qwen3.6-plus`
3. `trapi/mimo-v2.5`
4. `trapi/GLM-5V-Turbo`
5. `trapi/MiniMax-M3`

Skill 默认保留现有 imageModel；只有用户明确要求，或当前未配置且用户接受建议时才写入。

## 安全说明

- 不在聊天、命令参数、日志或文档中回显 API Key。
- 优先使用环境变量 SecretRef，不把真实 Key 明文写入 `openclaw.json`。
- 所有持久配置变更先展示差异并等待确认。
- 不使用 `--replace` 绕过 OpenClaw 的配置防覆盖保护。
- 不默认调用付费模型；实时验证前说明范围并征得确认。

详细参数见 [`references/custom-provider-config.md`](references/custom-provider-config.md)。

## 许可证

MIT
