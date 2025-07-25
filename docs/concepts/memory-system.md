# 让记忆永存：记忆与世界状态

一个有“灵魂”的机器人必须拥有记忆。YesImBot 的记忆系统旨在让机器人能够记住长期信息、理解短期对话上下文，并实现连贯、有深度的交流。

!!! warning "版本更新提醒"
    YesImBot v3 版本已**彻底废弃**了旧版的“记忆槽位 (Memory Slots)”和“场景 (Scenarios)”概念。如果您从旧版本升级，请务必采用新的记忆管理方式。

新的记忆系统由两大核心组件构成：**核心记忆 (Core Memory)** 和 **世界状态服务 (WorldStateService)**。

## 核心记忆 (Core Memory)

核心记忆是机器人的“**长期记忆**”和“**人格基石**”。它存储着那些稳定、持久、定义了机器人“是谁”的信息。

-   **实现方式:** 通过加载一个或多个本地 Markdown (`.md`) 文件来实现。
-   **配置路径:** `capabilities.memory.coreMemoryPath` (默认: `data/yesimbot/memory/core`)
-   **工作原理:**
    1.  在您指定的 `coreMemoryPath` 目录下，您可以创建任意数量的 `.md` 文件。
    2.  每个文件都代表一块独立的记忆，例如 `persona.md`（人格设定）, `knowledge.md`（知识库）。
    3.  YesImBot 在启动时会加载这些文件，并将其内容**永久注入**到 AI 的系统提示词中，作为其不可动摇的背景知识。
    4.  这使得您可以轻松地通过编辑文本文件来更新机器人的人格和核心知识库。

### 核心记忆文件格式与位置

-   **文件位置:** 记忆文件默认存放于 `data/yesimbot/memory/core/` 目录下。插件会自动加载此目录中所有格式正确的记忆文件。

-   **文件格式:** 文件必须使用 **YAML Front Matter** 来定义元信息，其下方为 Markdown 格式的记忆正文。

    ```yaml
    ---
    title: 核心人格 # (可选) 记忆的标题
    label: persona # (必填) 记忆的唯一标识符，用于内部引用
    limit: 200 # (必填) 此记忆块注入到Prompt中的最大Token限制
    description: 定义了Bot的核心人格和行为准则 # (可选) 描述信息
    ---

    这里是记忆的正文内容，使用 Markdown 格式编写。
    - 我的名字是小雅。
    - 我是一个乐于助人的 AI 助手。
    ```

-   **关键点:**
    -   !!! danger "必填字段"
        `label` 和 `limit` 为**必填字段**。缺少任何一个都会导致该文件在启动时加载失败，并会在控制台显示警告信息。
    -   **`label`**: 必须是唯一的，用于程序内部识别不同的记忆块。
    -   **`limit`**: 控制了此记忆块在最终构建提示词时所占用的最大 token 数量，防止超出模型限制。

## 世界状态服务 (WorldStateService)

世界状态服务是机器人的“**短期与中期记忆**”和“**对话上下文管理器**”。它解决了在长对话中，如何既不丢失关键信息，又不让上下文变得过长（超出 LLM 的 Token 限制）的核心矛盾。

-   **实现方式:** 一个自动化的对话历史管理服务。
-   **配置路径:** `capabilities.history`
-   **核心功能:**
    1.  **历史记录:** 忠实记录所有在允许频道内的对话。
    2.  **自动摘要 (Summarization):** 这是实现长期记忆的关键。当对话片段累计到一定数量 (`summarizationTriggerCount`) 时，`WorldStateService` 会自动调用一个专门的 AI 模型（由 `modelService.task.summarize` 指定），将这些“原始对话”压缩成一段简洁的“摘要记忆”。
    3.  **上下文构建:** 当需要与 AI 对话时，服务会智能地组合“**最新的几条原始对话**”和“**更早之前的对话摘要**”，形成一个既包含实时细节又包含历史脉络的完美上下文。

**关键配置项:**
-   `summarization.enabled`: 是否启用自动摘要功能。
-   `fullContextSegmentCount`: 在上下文中保留的最新“完整”对话片段数量。
-   `summarization.triggerCount`: 累计多少条对话后，触发一次摘要任务。

通过这种“**滚动摘要**”机制，YesImBot 理论上可以实现无限长度的对话记忆，确保了对话的深度和连贯性。

## 记忆系统配置

记忆系统提供了丰富的配置选项来优化性能和行为：

### 基础配置

```yaml
capabilities:
  memory:
    coreMemoryPath: "data/yesimbot/memory/core"  # 核心记忆文件路径
```

### 记忆衰减设置

```yaml
capabilities:
  memory:
    forgetting:
      checkIntervalHours: 24        # 遗忘检查周期（小时）
      stalenessDays: 90            # 多久未访问视为陈旧（天）
      salienceThreshold: 0.3       # 显著性阈值（0-1）
      accessCountThreshold: 2      # 访问次数阈值
```

### 用户画像生成设置

```yaml
capabilities:
  memory:
    profileGeneration:
      factRelevanceThreshold: 0.5   # 事实相关性阈值
      maxSummaryLength: 500         # 画像最大字符数
      updateIntervalHours: 24       # 画像更新间隔（小时）
      minFactsForUpdate: 5          # 触发更新的最小事实数
      confidenceThreshold: 0.7      # 置信度阈值
      enableIncrementalUpdate: true # 启用增量更新
      keyFactWeight: 2.0           # 关键事实权重倍数
```

### 缓存策略设置

```yaml
capabilities:
  memory:
    caching:
      enabled: true                    # 启用缓存
      profileCacheTtlMinutes: 30      # 用户画像缓存时间（分钟）
      factsCacheTtlMinutes: 15        # 用户事实缓存时间（分钟）
      maxCacheEntries: 1000           # 最大缓存条目数
      cleanupIntervalMinutes: 10      # 缓存清理间隔（分钟）
```

### 对话历史配置

```yaml
capabilities:
  history:
    summarization:
      enabled: true                   # 启用对话总结
      prompt: "..."                   # 总结提示词模板
      triggerCount: 6                 # 触发总结的片段数
      minTriggerMessages: 50          # 最少压缩消息数

    fullContextSegmentCount: 2        # 保留的完整片段数
    maxMessages: 30                   # 上下文最大消息数
    inactivityTimeoutSec: 1800        # 片段关闭超时（秒）

    recall:
      private: 3                      # 私聊召回画像数量
      guild: 8                        # 群组召回画像数量
      minConfidence: 0.5              # 最低置信度

    dataRetentionDays: 30             # 数据保留天数
    cleanupIntervalSec: 60            # 清理任务频率（秒）
```

## 最佳实践

### 1. 核心记忆文件组织

建议按功能分类创建记忆文件：

```
data/yesimbot/memory/core/
├── persona.md          # 基础人格设定
├── knowledge.md        # 专业知识库
├── rules.md           # 行为准则
└── context.md         # 特定场景信息
```

### 2. Token 限制管理

合理设置每个记忆块的 `limit` 值：
- 核心人格：200-300 tokens
- 知识库：500-1000 tokens
- 行为准则：100-200 tokens

### 3. 记忆衰减策略

根据使用场景调整遗忘参数：
- 活跃社群：较短的 `stalenessDays`
- 私人助手：较长的保留时间
- 临时服务：更激进的遗忘策略