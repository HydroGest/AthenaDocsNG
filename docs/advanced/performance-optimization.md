# 进阶玩法：性能与成本优化

当您的 YesImBot 在高流量环境中使用时，优化性能和控制 API 调用成本变得至关重要。以下是一些高级策略，可以帮助您实现这一目标。

## 1. 任务路由：最有效的成本控制

这是**最重要**的优化策略。不同的任务对模型能力的要求不同，通过为不同任务分配不同级别的模型，可以显著降低成本。

- **场景:** 聊天 (`chat`) 需要高质量的模型以保证体验，而后台的对话摘要 (`summarize`) 和文本嵌入 (`embed`) 任务则可以用更小、更便宜的模型完成。
- **实现:**
    1.  在 `modelService.providers` 中配置多种模型，例如一个昂贵的 `gpt-4o` 和一个廉价的本地 `llama3:8b`。
    2.  在 `modelService.modelGroups` 中创建两个独立的模型组：`premium_chat_group` (使用 `gpt-4o`) 和 `standard_task_group` (使用 `llama3:8b`)。
    3.  在 `modelService.task` 中进行分配：
        ```yaml
        task:
          chat: premium_chat_group
          summarize: standard_task_group
          embed: standard_task_group
        ```

## 2. 调整意愿系统：减少不必要的响应

机器人的每一次回复都可能产生 API 调用成本。通过调整意愿系统，可以减少非关键对话的发生。

- **策略:**
    - **提高响应阈值:** 增加 `agentBehavior.willingness.lifecycle.probabilityThreshold` 的值，使得机器人需要更高的意愿分数才会响应。
    - **增加发言成本:** 增加 `agentBehavior.willingness.lifecycle.replyCost` 的值，让机器人在发言后“精力”下降更多，从而减少连续发言的频率。
    - **收紧兴趣模型:** 减少 `agentBehavior.willingness.interest.keywords` 中的关键词数量，或降低 `keywordMultiplier`，让机器人只对自己最关心的领域发言。

## 3. 善用缓存

YesImBot 内置了缓存系统，可以减少对重复计算和 API 调用的需求。

- **配置路径:** `system.cache`
- **可调整项:**
    - `ttlSeconds`: 缓存的存活时间（秒）。如果您的机器人处理的信息时效性不强，可以适当增加此值。
    - `maxSize`: 缓存的最大项目数。增加此值会消耗更多内存，但能提高缓存命中率。
- **缓存内容:** 系统会缓存模型响应、记忆检索结果、工具执行结果等。

## 4. 优化日志级别

在生产环境中，过于详细的日志会带来不必要的磁盘 I/O 开销。

- **策略:** 在 `system.logging.level` 中，将日志级别设置为 `info` 或 `warn`。
    ```yaml
    system:
      logging:
        level: info
    ```
- **注意:** 仅在需要排查问题时，才将级别临时调整为 `debug` 或 `trace`。