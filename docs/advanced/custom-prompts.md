# 进阶玩法：定制提示词

提示词（Prompt）是塑造机器人人格、能力和行为方式的核心。YesImBot 提供了强大的提示词模板系统，让您可以深度定制与 AI 模型的每一次交互。

## 提示词配置

所有提示词相关的配置都位于 `agentBehavior.prompt` 下。

```yaml
agentBehavior:
  prompt:
    systemTemplate: "你是一个名为“{{session.bot.name}}”的 AI 助手。..."
    userTemplate: "当前时间是 {{time.full}}。用户 {{session.user.name}} (ID: {{session.user.id}}) 说：\n{{WORLD_STATE.channel.history.pending.dialogue.0.content}}"
    multiModalSystemTemplate: "..."
```

## 模板引擎与可用变量

YesImBot 使用 [Mustache](https://mustache.github.io/) 作为模板引擎。这意味着您可以在模板中使用 `{{variable}}` 或 `{{#section}}...{{/section}}` 语法来插入动态数据。

以下是渲染提示词时可用的主要数据对象（View）：

-   `{{#session}}`: Koishi 的会话对象，包含了机器人和用户信息。
    -   `{{session.bot.name}}`: 机器人的名字。
    -   `{{session.user.name}}`: 发送消息的用户的昵称。
    -   `{{session.user.id}}`: 用户的唯一 ID。
-   `{{#TOOL_DEFINITION}}`: 包含了所有可用工具的定义。
    -   `{{#tools}}...{{/tools}}`: 遍历所有工具。
-   `{{#CORE_MEMORY}}`: 核心记忆内容。
    -   `{{#memoryBlocks}}...{{/memoryBlocks}}`: 遍历所有记忆块。
-   `{{#WORLD_STATE}}`: 当前世界的完整快照。
    -   `{{WORLD_STATE.channel.name}}`: 当前频道名称。
    -   `{{#WORLD_STATE.channel.history.pending}}...{{/WORLD_STATE.channel.history.pending}}`: 当前正在处理的对话片段。
    -   `{{#WORLD_STATE.channel.history.closed}}...{{/WORLD_STATE.channel.history.closed}}`: 最近的已关闭对话历史。
    -   `{{#WORLD_STATE.channel.history.folded}}...{{/WORLD_STATE.channel.history.folded}}`: 更早的、被摘要的对话历史。
-   `{{#CURRENT_CONVERSATION}}`: 在 Agent 的一次回复中，之前心跳的思考和行动历史。
    -   `{{#history}}...{{/history}}`: 遍历之前的心跳记录。
-   `{{ONETIME_CODE}}`: 一个一次性的验证码，用于在多模态消息中安全地引用图片。
-   **全局 Snippets:**
    -   `{{currentTime}}`: 获取 ISO 格式的当前时间。

!!! tip "模板编写建议"
    建议参考 `packages/core/resources/templates/` 目录下的默认模板，它们是展示如何使用这些复杂数据结构的绝佳示例。

### 可用变量

以下是一些常用的可在提示词中使用的变量：

-   `{{bot.name}}`: 机器人的名字。
-   `{{time.full}}`: 当前的完整日期和时间。
-   `{{time.date}}`: 当前日期。
-   `{{time.time}}`: 当前时间。
-   `{{user.name}}`: 发送消息的用户的昵称。
-   `{{user.id}}`: 用户的唯一 ID。
-   `{{message.content}}`: 用户消息的纯文本内容。
-   `{{message.images.count}}`: 消息中包含的图片数量。
-   `{{roleplay.favor}}`: (需安装好感度插件) 当前用户的好感度数值。
-   `{{roleplay.state}}`: (需安装好感度插件) 当前用户的好感度阶段描述。

## 模板详解

### `system` 模板
- **作用:** 这是最重要的模板，用于定义机器人的**核心角色**、**行为准则**和**能力边界**。它在每次对话开始时发送给 AI。
- **最佳实践:**
    - **明确角色:** 开门见山地告诉 AI 它是谁。例如：“你是一个专业的编程助手。”
    - **设定规则:** 规定它应该做什么，不应该做什么。例如：“你的回答必须严谨、准确。不要闲聊。”
    - **描述能力:** 如果它有工具，可以在这里提及。例如：“你可以使用 `execute_javascript` 工具来运行代码。”

### `user` 模板
- **作用:** 用于包装用户的实际输入，并附加上下文信息。
- **最佳实践:**
    - 保留 `{{message.content}}` 是必须的。
    - 添加时间、用户信息等有助于 AI 更好地理解情境。

### `multimodal` 模板
- **作用:** 当用户的消息包含图片时，此模板的内容会附加在 `user` 模板之后，以提醒 AI 注意视觉信息。
- **最佳实践:**
    - 明确告知 AI 有图片存在，并可以引导它如何处理，例如：“请描述这些图片的内容。”

## 示例：创建一个“翻译专家”

假设您想让机器人成为一个只进行翻译的专家，您可以这样修改 `system` 模板：

```yaml
agentBehavior:
  prompt:
    system: |
      你是一个顶级的多语言翻译引擎。你的唯一任务是翻译用户提供的内容，不要进行任何解释、对话或评论。
      你的工作流程是：
      1. 自动检测用户输入的语言。
      2. 如果用户输入的是中文，将其翻译成英文。
      3. 如果用户输入的是其他语言，将其翻译成中文。
      4. 只输出翻译结果，不要包含任何额外文字，如“翻译结果是：”。
```