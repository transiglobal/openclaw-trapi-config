# trapi 模型与配置参考

## 目录

- [Provider 配置](#provider-配置)
- [预置模型](#预置模型)
- [别名](#别名)
- [图像模型](#图像模型)
- [当前 OpenClaw 写入方式](#当前-openclaw-写入方式)
- [故障排查](#故障排查)

## Provider 配置

trapi 使用混合模型适配器。Provider 级 `api` 只是默认值；模型级 `api` 优先。

```json
{
  "baseUrl": "https://lapi.transiglobal.com",
  "apiKey": {
    "source": "env",
    "provider": "default",
    "id": "TRAPI_API_KEY"
  },
  "api": "anthropic-messages"
}
```

不要在配置示例中放真实 API Key。

## 预置模型

所有模型暂时保留零费用元数据。不要给任何模型写 `reasoning` 字段，让 OpenClaw 和上游按模型默认行为处理。

| 模型 ID | API | 输入 | contextWindow | maxTokens |
|---|---|---|---:|---:|
| GLM-5-Turbo | anthropic-messages | text | 200000 | 64000 |
| GLM-5.2 | anthropic-messages | text | 200000 | 64000 |
| GLM-4.5-Air | anthropic-messages | text | 200000 | 64000 |
| MiniMax-M2 | anthropic-messages | text | 200000 | 64000 |
| MiniMax-M2.7 | anthropic-messages | text | 200000 | 64000 |
| MiniMax-M3 | anthropic-messages | text, image | 1000000 | 131072 |
| kimi-for-coding | anthropic-messages | text, image | 262144 | 32768 |
| GLM-5V-Turbo | anthropic-messages | text, image | 200000 | 64000 |
| PaddleOCR-VL-1.5 | anthropic-messages | text, image | 131072 | 8192 |
| deepseek-v4-pro | anthropic-messages | text | 1000000 | 384000 |
| deepseek-v4-flash | anthropic-messages | text | 1000000 | 384000 |
| deepseek-flash | anthropic-messages | text, image | 1000000 | 384000 |
| gpt-5.5 | openai-responses | text | 200000 | 65536 |
| gpt-5.6-sol | openai-responses | text, image | 372000 | 128000 |
| gpt-5.6-terra | openai-responses | text, image | 372000 | 128000 |
| gpt-5.6-luna | openai-responses | text, image | 372000 | 128000 |
| mimo-v2.5-pro | anthropic-messages | text | 200000 | 64000 |
| mimo-v2.5 | anthropic-messages | text, image, video, audio | 200000 | 64000 |
| claude-opus-4.7 | anthropic-messages | text | 300000 | 64000 |
| qwen3.7-max | anthropic-messages | text | 1000000 | 100000 |
| qwen3.6-plus | anthropic-messages | text, image | 1000000 | 100000 |

完整模型数组：

```json
[
  {"id":"GLM-5-Turbo","name":"GLM-5-Turbo (Transiglobal)","api":"anthropic-messages","input":["text"],"cost":{"input":0,"output":0,"cacheRead":0,"cacheWrite":0},"contextWindow":200000,"maxTokens":64000},
  {"id":"GLM-5.2","name":"GLM-5.2 (Transiglobal)","api":"anthropic-messages","input":["text"],"cost":{"input":0,"output":0,"cacheRead":0,"cacheWrite":0},"contextWindow":200000,"maxTokens":64000},
  {"id":"GLM-4.5-Air","name":"GLM-4.5-Air (Transiglobal)","api":"anthropic-messages","input":["text"],"cost":{"input":0,"output":0,"cacheRead":0,"cacheWrite":0},"contextWindow":200000,"maxTokens":64000},
  {"id":"MiniMax-M2","name":"MiniMax M2 (Transiglobal)","api":"anthropic-messages","input":["text"],"cost":{"input":0,"output":0,"cacheRead":0,"cacheWrite":0},"contextWindow":200000,"maxTokens":64000},
  {"id":"MiniMax-M2.7","name":"MiniMax-M2.7 (Transiglobal)","api":"anthropic-messages","input":["text"],"cost":{"input":0,"output":0,"cacheRead":0,"cacheWrite":0},"contextWindow":200000,"maxTokens":64000},
  {"id":"MiniMax-M3","name":"MiniMax M3 (Transiglobal)","api":"anthropic-messages","input":["text","image"],"cost":{"input":0,"output":0,"cacheRead":0,"cacheWrite":0},"contextWindow":1000000,"maxTokens":131072},
  {"id":"kimi-for-coding","name":"kimi-for-coding (Transiglobal)","api":"anthropic-messages","input":["text","image"],"cost":{"input":0,"output":0,"cacheRead":0,"cacheWrite":0},"contextWindow":262144,"maxTokens":32768},
  {"id":"GLM-5V-Turbo","name":"GLM-5V-Turbo (Transiglobal)","api":"anthropic-messages","input":["text","image"],"cost":{"input":0,"output":0,"cacheRead":0,"cacheWrite":0},"contextWindow":200000,"maxTokens":64000},
  {"id":"PaddleOCR-VL-1.5","name":"PaddleOCR-VL-1.5 (Transiglobal)","api":"anthropic-messages","input":["text","image"],"cost":{"input":0,"output":0,"cacheRead":0,"cacheWrite":0},"contextWindow":131072,"maxTokens":8192},
  {"id":"deepseek-v4-pro","name":"DeepSeek V4 Pro (Transiglobal)","api":"anthropic-messages","input":["text"],"cost":{"input":0,"output":0,"cacheRead":0,"cacheWrite":0},"contextWindow":1000000,"maxTokens":384000},
  {"id":"deepseek-v4-flash","name":"DeepSeek V4 Flash (Transiglobal)","api":"anthropic-messages","input":["text"],"cost":{"input":0,"output":0,"cacheRead":0,"cacheWrite":0},"contextWindow":1000000,"maxTokens":384000},
  {"id":"deepseek-flash","name":"DeepSeek Flash (Transiglobal)","api":"anthropic-messages","input":["text","image"],"cost":{"input":0,"output":0,"cacheRead":0,"cacheWrite":0},"contextWindow":1000000,"maxTokens":384000},
  {"id":"gpt-5.5","name":"GPT-5.5 (Transiglobal)","api":"openai-responses","input":["text"],"cost":{"input":0,"output":0,"cacheRead":0,"cacheWrite":0},"contextWindow":200000,"maxTokens":65536},
  {"id":"gpt-5.6-sol","name":"GPT-5.6 Sol (Transiglobal)","api":"openai-responses","input":["text","image"],"cost":{"input":0,"output":0,"cacheRead":0,"cacheWrite":0},"contextWindow":372000,"maxTokens":128000},
  {"id":"gpt-5.6-terra","name":"GPT-5.6 Terra (Transiglobal)","api":"openai-responses","input":["text","image"],"cost":{"input":0,"output":0,"cacheRead":0,"cacheWrite":0},"contextWindow":372000,"maxTokens":128000},
  {"id":"gpt-5.6-luna","name":"GPT-5.6 Luna (Transiglobal)","api":"openai-responses","input":["text","image"],"cost":{"input":0,"output":0,"cacheRead":0,"cacheWrite":0},"contextWindow":372000,"maxTokens":128000},
  {"id":"mimo-v2.5-pro","name":"MiMo 2.5 Pro (Transiglobal)","api":"anthropic-messages","input":["text"],"cost":{"input":0,"output":0,"cacheRead":0,"cacheWrite":0},"contextWindow":200000,"maxTokens":64000},
  {"id":"mimo-v2.5","name":"MiMo 2.5 (Transiglobal)","api":"anthropic-messages","input":["text","image","video","audio"],"cost":{"input":0,"output":0,"cacheRead":0,"cacheWrite":0},"contextWindow":200000,"maxTokens":64000},
  {"id":"claude-opus-4.7","name":"Claude Opus 4.7 (Transiglobal)","api":"anthropic-messages","input":["text"],"cost":{"input":0,"output":0,"cacheRead":0,"cacheWrite":0},"contextWindow":300000,"maxTokens":64000},
  {"id":"qwen3.7-max","name":"Qwen 3.7 Max (Transiglobal)","api":"anthropic-messages","input":["text"],"cost":{"input":0,"output":0,"cacheRead":0,"cacheWrite":0},"contextWindow":1000000,"maxTokens":100000},
  {"id":"qwen3.6-plus","name":"Qwen 3.6 Plus (Transiglobal)","api":"anthropic-messages","input":["text","image"],"cost":{"input":0,"output":0,"cacheRead":0,"cacheWrite":0},"contextWindow":1000000,"maxTokens":100000}
]
```

## 别名

| 模型 ID | 别名 |
|---|---|
| GLM-5-Turbo | glm5t |
| GLM-5.2 | glm52 |
| GLM-4.5-Air | glm45a |
| MiniMax-M2 | mxm2 |
| MiniMax-M2.7 | mxm27 |
| MiniMax-M3 | mxm3 |
| kimi-for-coding | kimi |
| GLM-5V-Turbo | glm5v |
| PaddleOCR-VL-1.5 | pocr |
| deepseek-v4-pro | dsv4p |
| deepseek-v4-flash | dsv4f |
| deepseek-flash | dsf |
| gpt-5.5 | gpt55 |
| gpt-5.6-sol | g56s |
| gpt-5.6-terra | g56t |
| gpt-5.6-luna | g56l |
| mimo-v2.5-pro | mimo25p |
| mimo-v2.5 | mimo25 |
| claude-opus-4.7 | opus47 |
| qwen3.7-max | qwn37 |
| qwen3.6-plus | qwn36 |
| openai/gpt-6-astra | gastra |

补充说明：`openai/gpt-6-astra` 属于 openai 预设 Provider（非 trapi），2026-09-10 已用 `--merge` 方式写入 `models.providers.openai.models`，参数与 gpt-5.6-sol 对齐（contextWindow 372000，maxTokens 128000，input: text, image），注册别名 `gastra`。

别名没有强制五字符限制。优先简短、清晰、稳定，不自动改动用户已有别名。

对话中使用：

```text
/model glm5t
/model mxm3
/model g56s
```

## 图像模型

支持图片输入：

- MiniMax-M3
- kimi-for-coding
- GLM-5V-Turbo
- PaddleOCR-VL-1.5
- deepseek-flash
- gpt-5.6-sol
- gpt-5.6-terra
- gpt-5.6-luna
- mimo-v2.5
- qwen3.6-plus

imageModel 仅用于图像理解，不等同于图像生成模型。

## 当前 OpenClaw 写入方式

优先使用：

```bash
openclaw config set models.providers.trapi '<provider-json>' --strict-json --merge
openclaw config set models.providers.trapi.models '<model-array-json>' --strict-json --merge
openclaw models aliases add <alias> trapi/<model-id>
openclaw models set-image trapi/<model-id>
openclaw models image-fallbacks add trapi/<model-id>
```

不要把模型或别名更新写成 Agent 侧 `gateway config.patch`。当前 OpenClaw 会保护全局模型配置路径，且 Patch 中数组不是按模型 ID 自动追加。

## 故障排查

- 401/403：检查 Gateway 服务是否能解析 `TRAPI_API_KEY` SecretRef。
- Model not found：核对模型 ID 大小写及 trapi 实际开放目录。
- 协议错误：核对模型级 `api`，GPT 模型使用 `openai-responses`，其余目录条目使用各自声明值。
- 别名冲突：执行 `openclaw models aliases list --json`，由用户决定保留或重定向。
- 配置写入被拒绝：重新读取目标路径并使用 `--merge`；不要直接改用 `--replace`。
- 图像未传入模型：确认模型 `input` 包含 `image`，并检查 imageModel 配置。
- `available: true` 但调用失败：该状态不证明上游推理可用，需要经用户确认后做最小实时请求。