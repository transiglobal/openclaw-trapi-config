---
name: "openclaw-trapi-config"
description: "配置传米 trapi Provider、模型、别名与图像模型；用于安装、增量更新、验证和排障，包括 MiniMax-M3、GPT-5.6 与 openai/gpt-6-astra。"
---

# trapi 配置

为 OpenClaw 配置传米科技 trapi Provider。trapi 允许不同模型使用不同 API 适配器；不要假设所有模型都使用同一种协议。

详细模型参数和别名见 [references/custom-provider-config.md](references/custom-provider-config.md)。配置前必须读取该文件。

## 固定信息

- Provider ID：`trapi`
- Base URL：`https://lapi.transiglobal.com`
- API Key 环境变量：`TRAPI_API_KEY`
- 默认适配器：`anthropic-messages`
- GPT 模型按目录使用 `openai-responses`

## 安全原则

1. 先读取并校验现有配置，再提出变更。
2. 只做增量写入；不得用不完整对象覆盖 Provider、模型数组、别名表或 imageModel。
3. 不在聊天、命令参数、日志或文档中回显 API Key。
4. 优先使用环境变量 SecretRef；不要把真实 Key 明文写入 `openclaw.json`。
5. 任何持久配置变更前，列出将新增、更新、保留和跳过的项目，并等待用户确认。
6. 不自动删除现有模型或别名。删除或替换必须由用户明确指定。
7. 不自动覆盖现有默认模型、imageModel 或 fallback 链。
8. 不使用 Agent 侧 `gateway config.patch` 写入 `models.providers` 等受保护路径。优先使用当前主机上的 OpenClaw CLI。
9. 不默认执行付费模型调用。实时验证前说明调用范围并征得确认。

## 工作流

### 1. 检查版本和 Schema

执行：

```bash
openclaw --version
openclaw config validate
openclaw config schema
```

要求当前版本支持：

- `openclaw config set ... --merge`
- `openclaw models aliases add`
- `openclaw models set-image`
- `openclaw models image-fallbacks`

如命令或 Schema 不支持这些字段，停止配置并建议使用 `openclaw update --yes` 升级。不要自行升级。

### 2. 检查现有状态

读取以下路径，不输出凭据值：

```bash
openclaw config get models.providers.trapi.models
openclaw config get agents.defaults.models
openclaw config get agents.defaults.imageModel
openclaw models list --provider trapi --json
openclaw models aliases list --json
openclaw models status --json
```

对照参考目录，按模型 ID 生成差异：

- 新增：目录有、当前没有
- 更新：模型已存在，但 api、input、contextWindow 或 maxTokens 不一致
- 保留：现有配置与目录一致
- 额外：当前存在、目录没有；只报告，不自动删除
- 别名缺失或冲突：单独列出

`GLM-5.1` 不再属于预置目录。若现有实例仍配置它，只列为额外项，不自动删除。

### 3. 处理认证

如果现有 trapi 认证可解析并且用户没有要求换 Key，保留现有认证，不再索要 Key。

首次配置或认证失效时，引导用户在 Gateway 服务环境中设置 `TRAPI_API_KEY`，然后使用 SecretRef：

```json
{
  "source": "env",
  "provider": "default",
  "id": "TRAPI_API_KEY"
}
```

配置前确认该环境变量对 Gateway 服务进程可见。不得要求用户把 Key 直接粘贴到群聊；私聊中也优先让用户在主机端设置。

### 4. 展示变更方案并确认

至少展示：

- Provider 是否新建
- 将新增和更新的模型
- 将新增或调整的别名
- 当前目录外模型
- imageModel 是否保持不变
- 是否需要 Gateway 重载或重启
- 是否计划进行实时模型调用

用户未确认时不得写入。

### 5. 安全增量写入

使用参考文件中的 Provider 和模型目录。

优先使用 CLI 的合并写入：

```bash
openclaw config set models.providers.trapi '<provider-json>' --strict-json --merge
openclaw config set models.providers.trapi.models '<model-array-json>' --strict-json --merge
```

规则：

- Provider 已存在时保留未请求修改的字段。
- `models.providers.trapi.models` 只提交完整的新条目或明确更新的条目，并使用 `--merge`。
- 如 CLI 检测到会删除条目并拒绝写入，重新读取配置并修正差异；不要改用 `--replace` 绕过保护。
- 不直接编辑 `openclaw.json`。
- 不使用全量 `config.apply`。

逐个注册缺失别名：

```bash
openclaw models aliases add <alias> trapi/<model-id>
```

如别名被其他模型占用，报告冲突并让用户决定；不要自动重定向已有别名。

### 6. 可选配置 imageModel

只有用户明确要求，或现有 imageModel 为空且用户接受建议时才配置。

建议链仅引用已配置且认证可用的 trapi 图像模型：

1. `trapi/kimi-for-coding`
2. `trapi/qwen3.6-plus`
3. `trapi/mimo-v2.5`
4. `trapi/GLM-5V-Turbo`
5. `trapi/MiniMax-M3`

使用：

```bash
openclaw models set-image trapi/kimi-for-coding
openclaw models image-fallbacks add trapi/qwen3.6-plus
```

后续 fallback 逐个添加。已有 imageModel 时默认保持原样，不加入未认证的其他 Provider 模型。

### 7. 静态验证

写入后执行：

```bash
openclaw config validate
openclaw models list --provider trapi --json
openclaw models aliases list --json
openclaw models status --json
openclaw gateway status
```

静态验证通过条件：

- 配置 Schema 有效
- 新增模型出现在 trapi 列表
- 别名指向正确模型
- Gateway 探针正常
- 没有凭据、模型或 imageModel 缺失警告

`models list` 的 `available: true` 只表示配置和认证状态可用，不等于已完成真实推理验证。

### 8. 可选实时验证

获得用户确认后，只验证本次新增或更新的模型，不默认全量测试。

文本模型使用最小请求：

```text
Reply with exactly: OK
```

图像模型还需用一张无敏感信息的测试图片验证图片输入。需要工具能力时，再单独做一次最小工具调用测试。

并发数控制在 2–3。记录成功、超时、认证失败、模型不存在和协议不兼容。不要把“返回任意文本”当作所有能力均验证通过。

### 9. 报告结果

输出以下内容：

- 已新增和更新的模型
- 已注册的别名
- 保留的现有配置
- 未处理的额外模型
- 静态验证结果
- 实时验证结果或“未执行”
- 失败项、原因和建议
- 正确切换方式：`/model <alias>`

不要声称可以直接输入 `/<alias>` 切换模型。
