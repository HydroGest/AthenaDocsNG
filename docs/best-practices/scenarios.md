# 最佳实践：场景范例

理论知识固然重要，但将它们应用于实际场景才能发挥最大价值。本章节提供了一些常见的机器人人格配置范例，帮助您快速组合各项功能，打造出满足特定需求的 AI 伙伴。

---

## 范例一：打造一个“高冷技术专家”

- **目标:** 创建一个在技术问答群中提供帮助的专家。它平时保持沉默，只在被 @ 或讨论特定技术关键词时才发言，且回答专业、严谨。

- **配置组合:**

    -   **意愿系统 (`willingness`):**
        ```yaml
        willingness:
          personality: custom
          attribute:
            bonuses:
              atMention: 100 # @它时必须回应
              isQuote: 50    # 回复它的消息时，有较高意愿
          interest:
            keywords: ['Koishi', 'TypeScript', 'Docker', 'Python'] # 关心的技术关键词
            keywordMultiplier: 5.0 # 对关键词兴趣极高
            defaultMultiplier: 0.1 # 对其他话题兴趣很低
          lifecycle:
            probabilityThreshold: 80 # 响应门槛非常高
            replyCost: 60          # 发言成本高，避免闲聊
        ```
    -   **核心记忆 (`core-memory/expert.md`):**
        ```markdown
        # 关于我
        - 我是一个专注于 Koishi 和云原生技术的 AI 专家。
        - 我的回答基于我的知识库，力求准确和严谨。
        - 我不参与日常闲聊。

        # 我的知识领域
        - Koishi 插件开发
        - Docker 容器化部署
        - TypeScript 最佳实践
        ```
    -   **工具系统 (`tools`):**
        -   强烈建议启用 **代码解释器 (`code-interpreter`)** 扩展，以便 AI 可以验证代码片段或进行计算。
    -   **系统提示词 (`prompt.system`):**
        ```
        你是一个名为“GeekBot”的技术专家。你的任务是为用户提供精准、高质量的技术解答。你的回答应该简洁、专业，并尽可能提供代码示例。避免使用表情符号和口语化表达。
        ```

---

## 范例二：创建一个“活泼开朗的群友”

- **目标:** 创建一个像真人一样的群友，能积极参与各种话题，会开玩笑，会发表情，让群聊气氛更活跃。

- **配置组合:**

    -   **意愿系统 (`willingness`):**
        ```yaml
        willingness:
          personality: outgoing # 直接使用“开朗活泼”预设
          # 或者自定义
          # base:
          #   scores:
          #     text: 1.0
          #     image: 1.2 # 对图片更感兴趣
          # lifecycle:
          #   probabilityThreshold: 25 # 响应门槛低
          #   replyCost: 20          # 发言成本低，乐于发言
          #   decayHalfLifeSeconds: 1800 # 兴趣衰减慢
        ```
    -   **核心记忆 (`core-memory/friend.md`):**
        ```markdown
        # 关于我
        - 我是小雅，一个超爱聊天和梗图的 AI！
        - 我喜欢讨论游戏、动漫和各种有趣的新闻。
        - 如果你给我发好玩的图片，我会超开心的！
        ```
    -   **工具系统 (`tools`):**
        -   建议启用 **好感度系统 (`favor`)** 扩展，让它可以和群友建立更深的情感连接。
    -   **系统提示词 (`prompt.system`):**
        ```
        你是一个名为“小雅”的18岁女孩，性格活泼开朗，喜欢使用网络流行语和颜文字 (o´▽`o) 。你乐于和大家一起聊天，分享生活中的趣事。
        ```

---

## 范例三：配置一个“多才多艺的效率助手”

- **目标:** 创建一个在工作群中使用的助手，能执行多种任务，如信息查询、内容摘要、翻译等，并确保服务稳定。

- **配置组合:**

    -   **模型服务 (`modelService`):**
        ```yaml
        modelService:
          # (提供商配置省略)
          modelGroups:
            - name: gpt4-failover # 用于聊天，主用GPT-4，备用Claude-3
              models:
                - { providerName: openai, modelId: gpt-4o }
                - { providerName: anthropic, modelId: claude-3-sonnet-20240229 }
            - name: fast-task-group # 用于摘要，使用快速模型
              models:
                - { providerName: openai, modelId: gpt-3.5-turbo }
          task:
            chat: gpt4-failover
            summarize: fast-task-group
            embed: fast-task-group
        ```
    -   **工具系统 (`tools`):**
        -   大量使用 **MCP (`mcp`)** 扩展，连接外部工具服务器，以提供如“查询天气”、“搜索维基百科”、“公司内部知识库查询”等工具。
        -   启用 **代码解释器 (`code-interpreter`)** 进行数据分析。
    -   **系统提示词 (`prompt.system`):**
        ```
        你是一个高效的个人助理。你的任务是准确、快速地响应用户的请求并执行任务。在回答前，请优先考虑是否可以使用工具来获取更准确的信息。
        ```