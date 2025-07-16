# Bot 的大脑：模型服务

模型服务 (`ModelService`) 是 YesImBot 的“大脑中枢”，它负责管理、连接和调用所有的大语言模型（LLM）。该服务取代了旧版的“适配器系统”，提供了更强大、更灵活的模型管理能力。

## 核心概念

模型服务的运作基于三个核心概念：**提供商 (Providers)**、**模型组 (Model Groups)** 和 **任务分配 (Task Assignment)**。

#### 1. 提供商 (Providers)
- **定义：** 一个“提供商”代表一个具体的 AI 模型 API 来源。
- **示例：** 你可以配置一个名为 `my_openai` 的 OpenAI 提供商，一个名为 `my_ollama` 的本地 Ollama 提供商，以及一个名为 `my_anthropic` 的 Anthropic 提供商。
- **配置：** `modelService.providers` 数组。每个对象包含 `name`, `type`, `apiKey`, `baseURL` 等信息。

#### 2. 模型组 (Model Groups)
- **定义：** 一个“模型组”是一个或多个具体模型的有序集合。这是实现**故障转移**和**任务路由**的关键。
- **工作原理：**
    - 当一个模型组被调用时，它会按照列表顺序尝试使用组内的模型。
    - 如果第一个模型调用失败（例如 API 宕机、并发超限），它会自动尝试列表中的下一个模型，保证服务的健壮性。
- **配置：** `modelService.modelGroups` 数组。

#### 3. 任务分配 (Task Assignment)
- **定义：** 将特定的内部“任务”分配给一个“模型组”。
- **核心任务类型：**
    - `chat`: 主要的对话功能。
    - `summarize`: 对话历史摘要任务。
    - `embed`: 文本嵌入任务（用于记忆检索等）。
- **优势：** 您可以为不同任务分配最适合的模型，以优化成本和性能。
    - **例如：** 使用最强大的 `gpt-4o` 进行聊天 (`chat`)，同时使用一个更便宜、更快速的本地模型 `llama3:8b` 来进行后台的摘要任务 (`summarize`)。
- **配置：** `modelService.task` 对象。

## 配置示例：实现故障转移与成本优化

以下配置展示了如何同时使用 OpenAI 和本地 Ollama，并为不同任务分配不同模型，实现高可用性和成本效益。

```yaml
modelService:
  providers:
    # 1. 配置 OpenAI 提供商
    - name: openai_provider
      type: OpenAI
      apiKey: sk-xxxxxxxxxxxxxxxxxxxxxxxxxxx # 你的 OpenAI Key
      models:
        - modelId: gpt-4o
        - modelId: gpt-3.5-turbo
    
    # 2. 配置本地 Ollama 提供商
    - name: ollama_provider
      type: Ollama
      baseURL: http://localhost:11434 # 你的 Ollama 地址
      models:
        - modelId: llama3:8b

  modelGroups:
    # 3. 创建一个用于聊天的模型组，具备故障转移能力
    - name: failover_chat_group
      models:
        - providerName: openai_provider   # 首选 gpt-4o
          modelId: gpt-4o
        - providerName: ollama_provider   # gpt-4o 失败后，自动切换到本地的 llama3
          modelId: llama3:8b
    
    # 4. 创建一个专门用于后台任务的模型组，节约成本
    - name: cheap_task_group
      models:
        - providerName: ollama_provider
          modelId: llama3:8b

  task:
    # 5. 为不同任务分配模型组
    chat: failover_chat_group      # 聊天使用高可用的 failover_chat_group
    summarize: cheap_task_group    # 摘要使用低成本的 cheap_task_group
    embed: cheap_task_group        # 嵌入也使用 cheap_task_group
```