# 完整配置参考

这是 YesImBot 的完整配置结构。您可以在 Koishi 的配置文件编辑器中找到这些选项。

## 根结构

```yaml
# AI 模型、API密钥和模型组配置
modelService: { ... }

# 智能体的性格、唤醒和响应逻辑
agentBehavior: { ... }

# 记忆、工具等扩展能力配置
capabilities: { ... }

# 图片服务配置
imageService: { ... }

# 系统缓存、调试等底层设置
system: { ... }
```

---

## `modelService`

AI 模型、API密钥和模型组配置。

| 键 | 类型 | 描述 | 必填 |
|---|---|---|---|
| `providers` | `ProviderConfig[]` | 配置你的 AI 模型提供商。 | 是 |
| `modelGroups` | `object[]` | 创建模型组，用于故障转移或任务路由。 | 是 |
| `task.chat` | string | 主要聊天功能使用的模型组名称。 | 是 |
| `task.embed` | string | 生成文本嵌入时使用的模型组名称。 | 是 |
| `task.summarize` | string | 对话历史总结时使用的模型组名称。 | 是 |

### `ProviderConfig` (在 `providers` 数组中)
| 键 | 类型 | 默认值 | 描述 |
|---|---|---|---|
| `name` | string | - | **提供商的唯一名称 (必填)**，用于在模型组中引用。 |
| `enabled` | boolean | `true` | 是否启用此提供商。 |
| `type` | string | `OpenAI` | 提供商类型, e.g., `OpenAI`, `Anthropic`, `Ollama`, `Google Gemini`。 |
| `baseURL` | string | - | API 地址。用于代理或本地模型。 |
| `apiKey` | string | - | 该服务的 API 密钥。 |
| `proxy` | string | - | 代理地址，如 `http://127.0.0.1:7890`。 |
| `models` | `ModelConfig[]` | **模型列表 (必填)**。 |

### `ModelConfig` (在 `models` 数组中)
| 键 | 类型 | 默认值 | 描述 |
|---|---|---|---|
| `modelId` | string | - | **模型ID (必填)**，如 `gpt-4o`。 |
| `abilities` | `string[]` | `[对话, 函数调用]` | 模型支持的能力。 |
| `parameters.temperature`| number | `1.36` | 温度参数，控制生成文本的随机性。 |
| `parameters.topP`| number | `0.8` | TopP 参数，控制核心词汇的范围。 |
| `parameters.stream`| boolean | `true` | 是否流式传输，可加快首响应时间。 |
| `parameters.custom`| `object`| - | 其他自定义模型参数。 |

### 模型组配置步骤

模型组的配置选项依赖于已有的模型，因此请**先配置好模型并成功启动一次插件**。

1.  **创建模型组**：在配置界面找到「模型组」(`modelGroups`)设置，点击“添加”创建新的一行，并展开进行编辑。
2.  **填写信息**：
    -   **模型组名称 (`name`)**：为其命名，例如 `default`。
    -   **选择模型 (`models`)**：点击“添加”，从下拉列表中选择要加入该组的模型。此列表会自动显示您在`providers`中已配置好的可用模型。
3.  **保存并应用**：保存配置并重载插件。新的模型组会立即生效，并可以被`task`任务分配所使用。

!!! tip "配置技巧"
    无论是选择模型还是模型组，都强烈建议**从下拉列表中选择预设项**，而不是手动输入名称，以避免因拼写错误导致配置失败。

---

## `agentBehavior`

智能体的性格、唤醒和响应逻辑。

| 键 | 类型 | 描述 |
|---|---|---|
| `arousal` | `ArousalConfig` | 唤醒条件，决定在哪些群聊中响应。 |
| `willingness` | `WillingnessConfig` | 响应意愿，决定何时响应。 |
| `heartbeat` | `number` | 每轮对话最大心跳次数 (默认 5)。 |
| `timeout` | `number` | 每轮对话最大超时时间 (秒, 默认 60)。 |
| `prompt` | `object` | 提示词模板。 |
| `vision` | `VisionConfig` | 视觉与多模态配置。 |

### `ArousalConfig`
| 键 | 类型 | 默认值 | 描述 |
|---|---|---|---|
| `allowedChannelGroups`| `object[][]`| `[]` | **允许 Agent 响应的频道 (必填)**。 |
| `debounceMs` | number | `1000` | 消息防抖时间 (毫秒)。 |

### `WillingnessConfig`
包含 `personality` 预设选项，以及 `base`, `attribute`, `interest`, `lifecycle` 四个部分的详细配置。详见[意愿系统](../concepts/willingness-system.md)章节。

### `VisionConfig`
| 键 | 类型 | 默认值 | 描述 |
|---|---|---|---|
| `enabled` | boolean | `false` | 是否启用视觉功能。 |
| `allowedImageTypes`| `string[]`| `["image/jpeg", "image/png"]` | 允许的图片MIME类型。 |
| `maxImagesInContext`| number | `3` | 上下文中允许的最大图片数。 |
| `imageLifecycleCount`| number | `2` | 图片在上下文中的生命周期。 |
| `detail` | string | `low` | 图片细节程度 (`low`, `high`, `auto`)。 |

---

## `capabilities`

记忆、工具等扩展能力配置。

| 键 | 类型 | 描述 |
|---|---|---|
| `memory` | `MemoryConfig` | 记忆能力配置。 |
| `tools` | `ToolServiceConfig` | 工具能力配置。 |
| `history` | `HistoryConfig` | 对话历史记录的管理方式。 |

### `MemoryConfig`
| 键 | 类型 | 默认值 | 描述 |
|---|---|---|---|
| `coreMemoryPath`| string | `data/yesimbot/memory/core`| 核心记忆 `.md` 文件的存放目录。 |
| `backup.enabled`| boolean| `false`| 是否启用归档记忆备份。 |
| `backup.backupPath`| string | `data/yesimbot/memory/backup`| 备份文件存放路径。 |

### `ToolServiceConfig`
| 键 | 类型 | 默认值 | 描述 |
|---|---|---|---|
| `extra` | `object` | `{}` | 用于存放各工具扩展的独立配置。 |
| `advanced.maxRetry`| number| `3`| 工具执行失败最大重试次数。 |
| `advanced.retryDelayMs`| number| `1000`| 重试延迟时间(毫秒)。 |
| `advanced.timeoutMs`| number| `10000`| 工具执行超时时间(毫秒)。 |
| `advanced.hotReload`| boolean| `false`| (开发用) 是否启用工具热重载。|
| `advanced.validateTypes`| boolean| `true`| 是否验证工具参数类型。|

### `HistoryConfig`
| 键 | 类型 | 默认值 | 描述 |
|---|---|---|---|
| `enableSummarization`| boolean| `true` | 是否启用对话历史自动总结功能。 |
| `summarizationPrompt`| string| (默认模板) | 用于生成对话摘要的提示词。 |
| `fullContextSegmentCount`| number| `2` | 保留的最新“完整”对话片段数。 |
| `summarizationTriggerCount`| number| `6` | 累计多少片段后触发一次总结。 |
| `advanced.maxHistoryItemsPerChannel`| number | `15`| 每个频道最大历史项目数。 |
| `advanced.maxMessages`| number| `30`| 上下文最大消息数。 |
| `advanced.dataRetentionDays`| number| `30`| 历史数据在数据库中保留天数。 |
| `advanced.cleanupIntervalMs`| number| `60000`| 后台清理任务频率(毫秒)。 |

---

## `imageService`
图片服务配置。

| 键 | 类型 | 默认值 | 描述 |
|---|---|---|---|
| `storagePath` | string | `data/yesimbot/images` | 图片本地存储路径。 |

---

## `system`
系统缓存、调试等底层设置。

| 键 | 类型 | 默认值 | 描述 |
|---|---|---|---|
| `cache.ttlSeconds`| number | `21600` | 缓存存活时间 (秒)。 |
| `cache.maxSize` | number | `1000` | 缓存最大项目数。 |
| `logging.level` | string | `info` | 日志级别 (`trace`, `debug`, `info`, `warn`, `error`)。 |
| `debug.enable`| boolean | `false`| 启用全局调试模式，会在控制台输出详细信息。 |
| `debug.uploadDump`| boolean | `false`| 应用出错时自动上报脱敏日志给开发者。 |