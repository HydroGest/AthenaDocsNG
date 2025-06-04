# 参考资料

本章节提供了 YesImBot 相关的参考资料，包括配置参数完整列表、API 参考、术语表和外部资源链接。这些资料可以帮助您更深入地了解 YesImBot 的工作原理和使用方法。

## 配置参数完整列表

以下是 YesImBot 所有配置参数的完整列表，按功能模块分类。

### 基本配置

| 参数名 | 类型 | 默认值 | 描述 |
|-------|------|-------|------|
| `enabled` | boolean | true | 是否启用 YesImBot 插件 |
| `debug.logLevel` | string | "info" | 日志级别：error, warn, info, verbose, debug |
| `debug.logToFile` | boolean | false | 是否将日志写入文件 |
| `debug.logFilePath` | string | "./logs/yesimbot.log" | 日志文件路径 |
| `debug.debugMode` | boolean | false | 是否启用调试模式 |
| `presets.use` | string | "" | 使用的预设配置名称 |
| `presets.customize` | boolean | true | 是否允许自定义预设配置 |

### 意愿值系统

| 参数名 | 类型 | 默认值 | 描述 |
|-------|------|-------|------|
| `parameters.willingness.enabled` | boolean | true | 是否启用意愿值系统 |
| `parameters.willingness.willingnessThreshold` | number | 0.7 | 回应意愿值阈值 |
| `parameters.willingness.baseWillingness` | number | 0.2 | 基础意愿值 |
| `parameters.willingness.nameMentionBoost` | number | 0.5 | 提及名字时的意愿值提升 |
| `parameters.willingness.keywordBoost` | number | 0.3 | 包含关键词时的意愿值提升 |
| `parameters.willingness.replyBoost` | number | 0.4 | 回复机器人消息时的意愿值提升 |
| `parameters.willingness.cooldownPeriod` | number | 60 | 冷却期（秒） |
| `parameters.willingness.groupActivityFactor` | number | 0.1 | 群组活跃度因子 |
| `parameters.willingness.randomFactor` | number | 0.1 | 随机因子 |
| `willingness.keywords` | string[] | [] | 触发关键词列表 |
| `willingness.ignoredUsers` | string[] | [] | 忽略的用户 ID 列表 |
| `willingness.ignoredGroups` | string[] | [] | 忽略的群组 ID 列表 |

### 记忆系统

| 参数名 | 类型 | 默认值 | 描述 |
|-------|------|-------|------|
| `parameters.memory.enabled` | boolean | true | 是否启用记忆系统 |
| `parameters.memory.maxMemoryTokens` | number | 4000 | 最大记忆令牌数 |
| `parameters.memory.contextWindow` | number | 10 | 上下文窗口大小 |
| `parameters.memory.memoryLifespan` | number | 24 | 记忆生命周期（小时） |
| `parameters.memory.importanceThreshold` | number | 0.5 | 记忆重要性阈值 |
| `parameters.memory.memoryCompression` | boolean | false | 是否启用记忆压缩 |
| `parameters.memory.compressionThreshold` | number | 0.8 | 压缩阈值 |
| `memory.slots` | object[] | [] | 记忆槽位配置 |
| `memory.slots[].name` | string | - | 槽位名称 |
| `memory.slots[].sessions` | string[] | [] | 关联的会话 ID 列表 |
| `memory.slots[].capacity` | number | 4000 | 槽位容量 |
| `memory.slots[].persistent` | boolean | false | 是否持久化存储 |
| `memory.slots[].storageType` | string | "memory" | 存储类型：memory, file, database |
| `memory.slots[].storagePath` | string | "" | 存储路径 |

### 适配器系统

| 参数名 | 类型 | 默认值 | 描述 |
|-------|------|-------|------|
| `adapters.enabled` | boolean | true | 是否启用多适配器系统 |
| `adapters.defaultAdapter` | string | "openai" | 默认适配器名称 |
| `adapters.loadBalancing.strategy` | string | "weighted_random" | 负载均衡策略 |
| `adapters.loadBalancing.failoverEnabled` | boolean | true | 是否启用故障转移 |
| `adapters.loadBalancing.maxRetries` | number | 3 | 最大重试次数 |
| `adapters.loadBalancing.retryDelay` | number | 1000 | 重试延迟（毫秒） |
| `adapters.adapterList` | object[] | [] | 适配器列表 |
| `adapters.adapterList[].name` | string | - | 适配器名称 |
| `adapters.adapterList[].type` | string | - | 适配器类型 |
| `adapters.adapterList[].enabled` | boolean | true | 是否启用该适配器 |
| `adapters.adapterList[].priority` | number | 5 | 适配器优先级 |
| `adapters.adapterList[].weight` | number | 1 | 负载均衡权重 |
| `adapters.adapterList[].config` | object | {} | 适配器特定配置 |

### 验证器系统

| 参数名 | 类型 | 默认值 | 描述 |
|-------|------|-------|------|
| `verifier.enabled` | boolean | true | 是否启用验证器系统 |
| `verifier.failureAction` | string | "regenerate" | 验证失败操作：regenerate, modify, ignore |
| `verifier.maxRetries` | number | 3 | 最大重试次数 |
| `verifier.timeout` | number | 5000 | 验证超时时间（毫秒） |
| `verifier.validators` | object[] | [] | 验证器列表 |
| `verifier.validators[].type` | string | - | 验证器类型 |
| `verifier.validators[].enabled` | boolean | true | 是否启用该验证器 |
| `verifier.validators[].priority` | number | 10 | 验证器优先级 |
| `verifier.validators[].settings` | object | {} | 验证器特定设置 |
| `verifier.validatorChain.executionMode` | string | "sequential" | 执行模式：sequential, parallel |

### 工具系统

| 参数名 | 类型 | 默认值 | 描述 |
|-------|------|-------|------|
| `toolSystem.enabled` | boolean | true | 是否启用工具系统 |
| `toolSystem.autoToolSelection` | boolean | true | 是否自动选择工具 |
| `toolSystem.maxToolsPerRequest` | number | 3 | 每个请求最多使用的工具数 |
| `toolSystem.toolExecutionTimeout` | number | 30000 | 工具执行超时时间（毫秒） |
| `toolSystem.includeToolResultsInContext` | boolean | true | 是否在上下文中包含工具结果 |
| `toolSystem.permissions.adminOnly` | boolean | false | 是否仅管理员可用 |
| `toolSystem.permissions.allowedUsers` | string[] | [] | 允许使用工具的用户列表 |
| `toolSystem.permissions.allowedGroups` | string[] | [] | 允许使用工具的群组列表 |
| `toolSystem.permissions.blockedUsers` | string[] | [] | 禁止使用工具的用户列表 |
| `toolSystem.permissions.blockedGroups` | string[] | [] | 禁止使用工具的群组列表 |
| `toolSystem.tools` | object | {} | 工具配置 |
| `toolSystem.customTools` | object[] | [] | 自定义工具列表 |

### 人格系统

| 参数名 | 类型 | 默认值 | 描述 |
|-------|------|-------|------|
| `personality.botName` | string | "Athena" | 机器人名称 |
| `personality.aliases` | string[] | [] | 机器人别名列表 |
| `personality.selfAwareness` | string | "AI助手" | 机器人的自我认知 |
| `personality.description` | string | "一个友好的AI助手" | 人格描述 |
| `personality.traits.friendliness` | number | 0.8 | 友好度（0-1） |
| `personality.traits.formality` | number | 0.5 | 正式度（0-1） |
| `personality.traits.creativity` | number | 0.7 | 创造力（0-1） |
| `personality.traits.humor` | number | 0.6 | 幽默感（0-1） |
| `personality.traits.patience` | number | 0.9 | 耐心度（0-1） |
| `personality.traits.assertiveness` | number | 0.4 | 自信度（0-1） |
| `personality.traits.empathy` | number | 0.8 | 共情能力（0-1） |
| `personality.traits.curiosity` | number | 0.7 | 好奇心（0-1） |
| `personality.communicationStyle` | object | {} | 交流风格配置 |
| `personality.background` | object | {} | 背景设定配置 |
| `personality.emotionSimulation` | object | {} | 情绪模拟配置 |
| `personality.responseModes` | object | {} | 响应模式配置 |
| `personality.useTemplate` | string | "" | 使用的人格模板 |
| `personality.customizeTemplate` | boolean | true | 是否自定义模板 |

### 提示词系统

| 参数名 | 类型 | 默认值 | 描述 |
|-------|------|-------|------|
| `prompts.systemPrompt` | string | "你是一个名为..." | 系统提示词 |
| `prompts.promptTemplate` | string | "..." | 提示词模板 |
| `prompts.scenarioPrompts` | object[] | [] | 场景提示词列表 |
| `prompts.scenarioPrompts[].name` | string | - | 场景名称 |
| `prompts.scenarioPrompts[].prompt` | string | - | 场景提示词 |
| `prompts.scenarioPrompts[].priority` | number | 5 | 场景优先级 |
| `prompts.scenarioPrompts[].conditions` | object | {} | 触发条件 |
| `prompts.customPrompts` | object | {} | 自定义提示词 |

### 图像功能

| 参数名 | 类型 | 默认值 | 描述 |
|-------|------|-------|------|
| `imageViewer.enabled` | boolean | true | 是否启用图像功能 |
| `imageViewer.viewing.enabled` | boolean | true | 是否启用图像查看 |
| `imageViewer.viewing.descriptionDetail` | string | "medium" | 描述详细程度：low, medium, high |
| `imageViewer.generation.enabled` | boolean | false | 是否启用图像生成 |
| `imageViewer.generation.provider` | string | "openai" | 图像生成提供商 |
| `imageViewer.generation.apiKey` | string | "" | API 密钥 |
| `imageViewer.generation.model` | string | "dall-e-3" | 模型 |
| `imageViewer.generation.size` | string | "1024x1024" | 图像大小 |
| `imageViewer.generation.quality` | string | "standard" | 质量：standard, hd |
| `imageViewer.generation.style` | string | "vivid" | 风格：vivid, natural |
| `imageViewer.generation.maxGenerationsPerDay` | number | 50 | 每日最大生成次数 |
| `imageViewer.supportedFormats` | string[] | ["jpg", "jpeg", "png", "gif", "webp"] | 支持的图像格式 |
| `imageViewer.storage.maxStorageSize` | number | 1024 | 最大存储大小（MB） |
| `imageViewer.storage.cleanupOldImages` | boolean | true | 是否清理旧图像 |
| `imageViewer.storage.imageRetentionDays` | number | 30 | 图像保留天数 |
| `imageViewer.storage.storagePath` | string | "./images" | 存储路径 |

### 性能参数

| 参数名 | 类型 | 默认值 | 描述 |
|-------|------|-------|------|
| `parameters.performance.cacheEnabled` | boolean | true | 是否启用缓存 |
| `parameters.performance.cacheSize` | number | 100 | 缓存大小 |
| `parameters.performance.cacheTTL` | number | 3600 | 缓存生存时间（秒） |
| `parameters.performance.batchSize` | number | 5 | 批处理大小 |
| `parameters.performance.parallelRequests` | number | 3 | 并行请求数 |
| `parameters.performance.requestInterval` | number | 0 | 请求间隔（毫秒） |
| `parameters.performance.timeout` | number | 30000 | 请求超时时间（毫秒） |

### 模型参数

| 参数名 | 类型 | 默认值 | 描述 |
|-------|------|-------|------|
| `parameters.model.temperature` | number | 0.7 | 温度参数 |
| `parameters.model.topP` | number | 0.9 | Top-P 采样参数 |
| `parameters.model.maxTokens` | number | 1000 | 最大令牌数 |
| `parameters.model.frequencyPenalty` | number | 0.5 | 频率惩罚参数 |
| `parameters.model.presencePenalty` | number | 0.5 | 存在惩罚参数 |
| `parameters.model.stopSequences` | string[] | [] | 停止序列 |

### 交互参数

| 参数名 | 类型 | 默认值 | 描述 |
|-------|------|-------|------|
| `parameters.interaction.responseDelay` | boolean | true | 是否启用响应延迟 |
| `parameters.interaction.minDelay` | number | 500 | 最小延迟（毫秒） |
| `parameters.interaction.maxDelay` | number | 2000 | 最大延迟（毫秒） |
| `parameters.interaction.typingIndicator` | boolean | true | 是否显示输入指示器 |
| `parameters.interaction.splitLongMessages` | boolean | true | 是否拆分长消息 |
| `parameters.interaction.maxMessageLength` | number | 2000 | 最大消息长度 |

### 维护参数

| 参数名 | 类型 | 默认值 | 描述 |
|-------|------|-------|------|
| `maintenance.autoCleanMemory` | boolean | true | 是否自动清理记忆 |
| `maintenance.cleanInterval` | number | 86400 | 清理间隔（秒） |
| `maintenance.backupEnabled` | boolean | false | 是否启用备份 |
| `maintenance.backupInterval` | number | 604800 | 备份间隔（秒） |
| `maintenance.backupPath` | string | "./backups" | 备份路径 |
| `maintenance.maxBackups` | number | 5 | 最大备份数量 |

## API 参考

### 核心 API

#### YesImBot 核心

```typescript
// 获取 YesImBot 实例
const yesimbot = ctx.yesimbot

// 获取版本信息
const version = yesimbot.version

// 重新加载配置
await yesimbot.reloadConfig()

// 获取状态信息
const status = yesimbot.getStatus()

// 重置状态
await yesimbot.reset()
```

#### 意愿值系统

```typescript
// 获取意愿值系统
const willingnessSystem = yesimbot.willingnessSystem

// 计算意愿值
const willingness = willingnessSystem.calculateWillingness(message)

// 检查是否应该回应
const shouldRespond = willingnessSystem.shouldRespond(message)

// 更新意愿值配置
willingnessSystem.updateConfig(config)

// 获取意愿值统计信息
const stats = willingnessSystem.getStats()
```

#### 记忆系统

```typescript
// 获取记忆系统
const memorySystem = yesimbot.memorySystem

// 添加记忆
await memorySystem.addMemory(session, content, importance)

// 获取相关记忆
const memories = await memorySystem.getRelevantMemories(session, query, limit)

// 清理旧记忆
const cleanedCount = await memorySystem.cleanupOldMemories(session, olderThan)

// 压缩记忆
await memorySystem.compressMemories(session)

// 获取记忆统计信息
const stats = memorySystem.getStats(session)
```

#### 适配器系统

```typescript
// 获取适配器系统
const adapterSystem = yesimbot.adapterSystem

// 发送请求到模型
const response = await adapterSystem.sendModelRequest(prompt, options)

// 获取可用适配器列表
const adapters = adapterSystem.getAvailableAdapters()

// 设置默认适配器
adapterSystem.setDefaultAdapter(adapterName)

// 获取适配器统计信息
const stats = adapterSystem.getStats()
```

#### 验证器系统

```typescript
// 获取验证器系统
const verifierSystem = yesimbot.verifierSystem

// 验证响应内容
const result = await verifierSystem.validateResponse(response, context)

// 添加自定义验证器
verifierSystem.addValidator(validator)

// 获取验证统计信息
const stats = verifierSystem.getStats()
```

#### 工具系统

```typescript
// 获取工具系统
const toolSystem = yesimbot.toolSystem

// 注册新工具
toolSystem.registerTool(tool)

// 执行工具
const result = await toolSystem.executeTool(toolName, params)

// 检查工具权限
const hasPermission = toolSystem.checkPermission(userId, toolName)

// 获取可用工具列表
const tools = toolSystem.getAvailableTools(userId)
```

#### 人格系统

```typescript
// 获取人格系统
const personalitySystem = yesimbot.personalitySystem

// 获取人格配置
const personality = personalitySystem.getPersonality()

// 更新人格配置
personalitySystem.updatePersonality(config)

// 应用人格模板
personalitySystem.applyTemplate(templateName)

// 获取当前情绪状态
const mood = personalitySystem.getCurrentMood()
```

### 事件

YesImBot 触发的事件：

```typescript
// 意愿值计算事件
ctx.on('yesimbot/willingness-calculated', (session, willingness) => {
  console.log(`计算的意愿值: ${willingness}`)
})

// 响应生成前事件
ctx.on('yesimbot/before-response', (session, context) => {
  console.log('准备生成响应')
})

// 响应生成后事件
ctx.on('yesimbot/after-response', (session, response) => {
  console.log(`生成的响应: ${response}`)
})

// 验证失败事件
ctx.on('yesimbot/validation-failed', (session, response, reason) => {
  console.log(`验证失败: ${reason}`)
})

// 工具执行事件
ctx.on('yesimbot/tool-executed', (session, toolName, result) => {
  console.log(`工具 ${toolName} 执行结果: ${JSON.stringify(result)}`)
})

// 记忆添加事件
ctx.on('yesimbot/memory-added', (session, memory) => {
  console.log(`添加记忆: ${memory.content}`)
})
```

### 中间件

YesImBot 提供的中间件：

```typescript
// 意愿值中间件
ctx.middleware(yesimbot.middleware.willingness())

// 记忆中间件
ctx.middleware(yesimbot.middleware.memory())

// 响应中间件
ctx.middleware(yesimbot.middleware.response())

// 工具中间件
ctx.middleware(yesimbot.middleware.tools())

// 自定义中间件示例
ctx.middleware((session, next) => {
  // 在这里处理消息
  return next()
})
```

## 术语表

| 术语 | 描述 |
|------|------|
| **意愿值** | 机器人回应特定消息的倾向程度，由多种因素计算得出 |
| **意愿值阈值** | 触发机器人回应所需的最小意愿值 |
| **记忆槽位** | 存储特定会话或群组对话历史的容器 |
| **记忆压缩** | 将长对话历史压缩为更简洁的摘要，以节省令牌使用 |
| **适配器** | 连接不同语言模型 API 的接口组件 |
| **负载均衡** | 在多个适配器之间分配请求的策略 |
| **故障转移** | 当一个适配器失败时自动切换到另一个适配器的机制 |
| **验证器** | 检查和过滤模型响应的组件 |
| **验证器链** | 按特定顺序执行的多个验证器 |
| **工具** | 扩展机器人功能的组件，如搜索、计算器等 |
| **提示词** | 发送给语言模型的指令和上下文 |
| **系统提示词** | 定义机器人基本行为和人格的核心提示词 |
| **场景提示词** | 针对特定场景或任务的专用提示词 |
| **人格特质** | 定义机器人性格和行为模式的参数 |
| **情绪模拟** | 模拟机器人情绪状态和变化的功能 |
| **响应模式** | 机器人生成回应的不同风格和方法 |
| **令牌** | 语言模型处理文本的基本单位，通常是单词或部分单词 |
| **上下文窗口** | 包含在当前对话中的消息数量 |
| **批处理** | 将多个请求合并为一个批次处理的技术 |
| **缓存** | 存储常见请求和响应以提高性能的机制 |

## 外部资源链接

### 官方资源

- [YesImBot GitHub 仓库](https://github.com/HydroGest/YesImBot)
- [YesImBot 官方文档](https://github.com/HydroGest/YesImBot/wiki)
- [问题报告](https://github.com/HydroGest/YesImBot/issues)
- [功能请求](https://github.com/HydroGest/YesImBot/issues/new?template=feature_request.md)

### 社区资源

- [YesImBot 插件目录](https://github.com/HydroGest/YesImBot-plugins)
- [YesImBot 示例项目](https://github.com/HydroGest/YesImBot-examples)
- [QQ 交流群](https://qm.qq.com/cgi-bin/qm/qr?k=857518324)
- [Discord 服务器](https://discord.gg/yesimbot)

### 相关项目

- [Koishi 框架](https://koishi.chat/)
- [OpenAI API 文档](https://platform.openai.com/docs/api-reference)
- [Ollama 项目](https://ollama.ai/)
- [Anthropic Claude API](https://docs.anthropic.com/claude/reference/getting-started-with-the-api)

### 学习资源

- [大型语言模型提示工程指南](https://www.promptingguide.ai/)
- [Koishi 插件开发指南](https://koishi.chat/zh-CN/guide/plugin/)
- [TypeScript 官方文档](https://www.typescriptlang.org/docs/)
- [Node.js 最佳实践](https://github.com/goldbergyoni/nodebestpractices)
