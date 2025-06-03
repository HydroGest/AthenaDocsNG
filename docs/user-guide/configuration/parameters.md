# 参数调优

参数调优是优化 YesImBot 性能和行为的关键环节。本章将详细介绍如何调整各种参数，以获得最佳的用户体验和机器人表现。

## 参数调优概述

YesImBot 提供了丰富的参数设置，允许用户根据实际需求和场景调整机器人的行为。这些参数主要分为以下几类：

1. **意愿值参数**：控制机器人的主动性和回应频率
2. **模型参数**：影响语言模型生成内容的特性
3. **记忆参数**：管理机器人的记忆和上下文理解
4. **交互参数**：调整机器人与用户的交互方式
5. **性能参数**：优化系统资源使用和响应速度

## 意愿值参数调优

意愿值系统是 YesImBot 的核心特性，通过调整以下参数可以控制机器人的主动性：

```yaml
Parameters:
  Willingness:
    BaseWillingness: 0.2          # 基础意愿值
    WillingnessThreshold: 0.7     # 意愿值阈值
    NameMentionBoost: 0.5         # 名字提及提升值
    KeywordBoost: 0.3             # 关键词提升值
    ReplyToMeBoost: 0.6           # 回复提升值
    CooldownPeriod: 60            # 冷却期（秒）
    ActivityFactor: 0.1           # 活跃度因子
```

### 参数说明

| 参数名 | 描述 | 默认值 | 推荐范围 | 影响 |
|-------|------|-------|---------|------|
| `BaseWillingness` | 基础意愿值，表示机器人对一般消息的兴趣程度 | 0.2 | 0.1-0.3 | 值越高，机器人越主动 |
| `WillingnessThreshold` | 意愿值阈值，超过此值时机器人会回应 | 0.7 | 0.6-0.8 | 值越低，机器人越容易回应 |
| `NameMentionBoost` | 当消息中提到机器人名字时的意愿值提升 | 0.5 | 0.4-0.6 | 值越高，机器人对名字提及越敏感 |
| `KeywordBoost` | 当消息中包含关键词时的意愿值提升 | 0.3 | 0.2-0.4 | 值越高，机器人对关键词越敏感 |
| `ReplyToMeBoost` | 当消息是对机器人回复的回应时的意愿值提升 | 0.6 | 0.5-0.7 | 值越高，机器人越倾向于继续对话 |
| `CooldownPeriod` | 回应后的冷却期（秒） | 60 | 30-120 | 值越高，机器人连续发言的可能性越低 |
| `ActivityFactor` | 群聊活跃度对意愿值的影响因子 | 0.1 | 0.05-0.2 | 值越高，群聊活跃度对意愿值的影响越大 |

### 调优策略

1. **活跃型机器人**：降低 `WillingnessThreshold`，提高 `BaseWillingness`，减少 `CooldownPeriod`
2. **被动型机器人**：提高 `WillingnessThreshold`，降低 `BaseWillingness`，增加 `CooldownPeriod`
3. **平衡型机器人**：使用默认值，根据实际表现微调

## 模型参数调优

模型参数影响语言模型生成内容的特性，通过调整以下参数可以控制回应的风格和质量：

```yaml
Parameters:
  Model:
    Temperature: 0.7              # 温度参数
    MaxTokens: 1000               # 最大令牌数
    TopP: 0.9                     # Top-p 采样参数
    FrequencyPenalty: 0.5         # 频率惩罚参数
    PresencePenalty: 0.5          # 存在惩罚参数
```

### 参数说明

| 参数名 | 描述 | 默认值 | 推荐范围 | 影响 |
|-------|------|-------|---------|------|
| `Temperature` | 控制生成文本的随机性，值越高随机性越大 | 0.7 | 0.1-1.0 | 值越高，回应越多样化但可能不一致 |
| `MaxTokens` | 单次生成的最大令牌数 | 1000 | 100-4000 | 值越高，回应可能越长 |
| `TopP` | 控制生成文本的多样性，值越低越保守 | 0.9 | 0.1-1.0 | 值越高，回应越多样化 |
| `FrequencyPenalty` | 降低模型重复使用相同词语的倾向 | 0.5 | 0.0-2.0 | 值越高，回应中重复词语越少 |
| `PresencePenalty` | 降低模型重复讨论相同主题的倾向 | 0.5 | 0.0-2.0 | 值越高，回应中重复主题越少 |

### 调优策略

1. **创意型回应**：提高 `Temperature`（0.8-1.0），提高 `TopP`（0.9-1.0）
2. **确定性回应**：降低 `Temperature`（0.1-0.4），降低 `TopP`（0.1-0.5）
3. **平衡型回应**：使用中等值（`Temperature` 0.5-0.7，`TopP` 0.7-0.9）
4. **避免重复**：提高 `FrequencyPenalty` 和 `PresencePenalty`（1.0-2.0）

## 记忆参数调优

记忆参数控制机器人的记忆管理和上下文理解能力：

```yaml
Parameters:
  Memory:
    MaxMemoryTokens: 4000         # 最大记忆令牌数
    MemoryLifespan: 24            # 记忆生命周期（小时）
    ImportanceThreshold: 0.5      # 记忆重要性阈值
    ContextWindow: 10             # 上下文窗口大小
    MemoryCompression: true       # 是否启用记忆压缩
    CompressionThreshold: 0.8     # 压缩阈值
```

### 参数说明

| 参数名 | 描述 | 默认值 | 推荐范围 | 影响 |
|-------|------|-------|---------|------|
| `MaxMemoryTokens` | 记忆槽位的最大令牌数 | 4000 | 2000-8000 | 值越高，机器人能记住更多内容 |
| `MemoryLifespan` | 记忆的生命周期（小时） | 24 | 12-72 | 值越高，记忆保留时间越长 |
| `ImportanceThreshold` | 记忆重要性阈值 | 0.5 | 0.3-0.7 | 值越低，保留的记忆越多 |
| `ContextWindow` | 上下文窗口大小（最近的消息数量） | 10 | 5-20 | 值越高，考虑的近期消息越多 |
| `MemoryCompression` | 是否启用记忆压缩 | true | - | 启用可节省令牌使用 |
| `CompressionThreshold` | 压缩阈值 | 0.8 | 0.7-0.9 | 值越低，越早触发压缩 |

### 调优策略

1. **长期记忆**：增加 `MaxMemoryTokens`（6000-8000），增加 `MemoryLifespan`（48-72）
2. **短期记忆**：减少 `MaxMemoryTokens`（2000-3000），减少 `MemoryLifespan`（12-24）
3. **高质量记忆**：提高 `ImportanceThreshold`（0.6-0.7）
4. **广泛记忆**：降低 `ImportanceThreshold`（0.3-0.4）
5. **资源优化**：启用 `MemoryCompression`，降低 `CompressionThreshold`（0.7）

## 交互参数调优

交互参数调整机器人与用户的交互方式：

```yaml
Parameters:
  Interaction:
    ResponseDelay: true           # 是否启用回应延迟
    BaseDelay: 2                  # 基础延迟（秒）
    DelayPerChar: 0.05            # 每字符延迟（秒）
    MaxDelay: 15                  # 最大延迟（秒）
    EmotionSimulation: true       # 是否启用情绪模拟
    EmotionChangeRate: 0.3        # 情绪变化速率
```

### 参数说明

| 参数名 | 描述 | 默认值 | 推荐范围 | 影响 |
|-------|------|-------|---------|------|
| `ResponseDelay` | 是否启用回应延迟 | true | - | 启用使交互更自然 |
| `BaseDelay` | 基础延迟（秒） | 2 | 1-5 | 值越高，初始延迟越长 |
| `DelayPerChar` | 每字符延迟（秒） | 0.05 | 0.01-0.1 | 值越高，打字速度越慢 |
| `MaxDelay` | 最大延迟（秒） | 15 | 5-30 | 限制最长延迟时间 |
| `EmotionSimulation` | 是否启用情绪模拟 | true | - | 启用使交互更人性化 |
| `EmotionChangeRate` | 情绪变化速率 | 0.3 | 0.1-0.5 | 值越高，情绪变化越快 |

### 调优策略

1. **自然交互**：启用 `ResponseDelay`，使用中等 `BaseDelay`（2-3）和 `DelayPerChar`（0.03-0.07）
2. **快速响应**：禁用 `ResponseDelay` 或使用较低的延迟参数
3. **情感丰富**：启用 `EmotionSimulation`，使用较高的 `EmotionChangeRate`（0.4-0.5）
4. **情感稳定**：禁用 `EmotionSimulation` 或使用较低的 `EmotionChangeRate`（0.1-0.2）

## 性能参数调优

性能参数优化系统资源使用和响应速度：

```yaml
Parameters:
  Performance:
    CacheEnabled: true            # 是否启用缓存
    CacheSize: 100                # 缓存大小（条目数）
    CacheExpiry: 3600             # 缓存过期时间（秒）
    BatchProcessing: true         # 是否启用批处理
    BatchSize: 5                  # 批处理大小
    ParallelRequests: 3           # 并行请求数
```

### 参数说明

| 参数名 | 描述 | 默认值 | 推荐范围 | 影响 |
|-------|------|-------|---------|------|
| `CacheEnabled` | 是否启用缓存 | true | - | 启用可减少重复请求 |
| `CacheSize` | 缓存大小（条目数） | 100 | 50-500 | 值越高，缓存越多内容 |
| `CacheExpiry` | 缓存过期时间（秒） | 3600 | 1800-86400 | 值越高，缓存保留越久 |
| `BatchProcessing` | 是否启用批处理 | true | - | 启用可提高处理效率 |
| `BatchSize` | 批处理大小 | 5 | 2-10 | 值越高，批处理越多消息 |
| `ParallelRequests` | 并行请求数 | 3 | 1-5 | 值越高，并行处理越多请求 |

### 调优策略

1. **高性能**：启用所有性能优化，使用较大的 `CacheSize`（200-500）和 `BatchSize`（8-10）
2. **低资源消耗**：使用较小的 `CacheSize`（50-100）和 `BatchSize`（2-3）
3. **实时响应**：减少 `CacheExpiry`（1800-3600），增加 `ParallelRequests`（4-5）
4. **长期缓存**：增加 `CacheExpiry`（43200-86400）

## 综合调优示例

以下是几种常见场景的综合调优示例：

### 活跃社区群聊

```yaml
Parameters:
  Willingness:
    BaseWillingness: 0.25
    WillingnessThreshold: 0.65
    CooldownPeriod: 45
  Model:
    Temperature: 0.8
    MaxTokens: 800
    FrequencyPenalty: 0.7
  Memory:
    MaxMemoryTokens: 5000
    MemoryLifespan: 36
    ContextWindow: 15
  Interaction:
    ResponseDelay: true
    BaseDelay: 1.5
    MaxDelay: 10
  Performance:
    BatchSize: 8
    ParallelRequests: 4
```

### 技术讨论群

```yaml
Parameters:
  Willingness:
    BaseWillingness: 0.15
    WillingnessThreshold: 0.75
    KeywordBoost: 0.4
  Model:
    Temperature: 0.4
    MaxTokens: 1500
    FrequencyPenalty: 0.3
  Memory:
    MaxMemoryTokens: 7000
    MemoryLifespan: 48
    ImportanceThreshold: 0.4
  Interaction:
    ResponseDelay: true
    BaseDelay: 3
    MaxDelay: 20
  Performance:
    CacheSize: 200
    CacheExpiry: 7200
```

### 私聊助手

```yaml
Parameters:
  Willingness:
    BaseWillingness: 0.5
    WillingnessThreshold: 0.5
    CooldownPeriod: 10
  Model:
    Temperature: 0.6
    MaxTokens: 2000
    PresencePenalty: 0.7
  Memory:
    MaxMemoryTokens: 6000
    MemoryLifespan: 72
    ImportanceThreshold: 0.3
  Interaction:
    ResponseDelay: true
    BaseDelay: 1
    MaxDelay: 8
  Performance:
    ParallelRequests: 1
```

## 参数调优工具

YesImBot 提供了一些工具，帮助用户更容易地调优参数：

### 参数预设

您可以使用预定义的参数预设，快速应用适合特定场景的参数组合：

```yaml
Parameters:
  Presets:
    - Name: "活跃社区"
      Description: "适合活跃的社区群聊，机器人较为主动"
      # 预设参数...
    
    - Name: "技术讨论"
      Description: "适合技术讨论群，回答更加准确和详细"
      # 预设参数...
    
    - Name: "私聊助手"
      Description: "适合一对一私聊，响应更快更全面"
      # 预设参数...
```

### 参数测试

您可以使用参数测试功能，在不影响生产环境的情况下测试不同的参数组合：

1. 在 Koishi 控制台中，导航到 YesImBot 插件配置
2. 找到"参数测试"部分
3. 调整测试参数
4. 点击"开始测试"
5. 查看测试结果和建议

## 最佳实践

1. **逐步调整**：一次只修改少量参数，然后观察效果
2. **记录变化**：记录参数调整和效果，以便找到最佳组合
3. **考虑场景**：根据群聊的性质和目的选择合适的参数
4. **定期优化**：随着群聊的发展和变化，定期重新评估和调整参数
5. **收集反馈**：关注用户反馈，了解他们对机器人行为的感受
6. **平衡资源**：在性能和功能之间找到平衡，避免过度消耗资源

## 常见问题

### 机器人过于活跃

如果机器人回应过于频繁，可以尝试：

- 提高 `WillingnessThreshold`（0.75-0.8）
- 降低 `BaseWillingness`（0.1-0.15）
- 增加 `CooldownPeriod`（90-120）

### 机器人过于沉默

如果机器人几乎不回应，可以尝试：

- 降低 `WillingnessThreshold`（0.5-0.6）
- 提高 `BaseWillingness`（0.25-0.3）
- 提高 `KeywordBoost` 和 `NameMentionBoost`
- 减少 `CooldownPeriod`（30-45）

### 回应质量不佳

如果机器人的回应质量不佳，可以尝试：

- 调整 `Temperature`（降低可获得更确定性的回应）
- 增加 `MaxMemoryTokens` 提供更多上下文
- 优化提示词设置
- 降低 `ImportanceThreshold` 保留更多记忆

### 系统性能问题

如果系统性能出现问题，可以尝试：

- 减少 `MaxMemoryTokens`
- 启用 `MemoryCompression`
- 减小 `CacheSize` 和 `BatchSize`
- 减少 `ParallelRequests`
