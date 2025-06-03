# 多适配器支持

YesImBot 的多适配器支持是其核心功能之一，允许系统连接和使用多种不同的语言模型 API。本章将详细介绍多适配器系统的工作原理、配置方法和最佳实践，帮助您充分利用这一强大功能。

## 多适配器概述

多适配器系统允许 YesImBot 同时连接多个语言模型 API，实现以下关键功能：

1. **负载均衡**：在多个 API 之间分配请求，优化资源使用
2. **故障转移**：当某个 API 失败时自动切换到备用 API，提高系统可靠性
3. **模型选择**：根据任务需求选择最合适的模型，提高回应质量
4. **成本优化**：根据价格和性能平衡使用不同的 API，控制运营成本

## 支持的适配器

YesImBot 目前支持以下语言模型适配器：

### OpenAI 适配器

连接 OpenAI 的 GPT 系列模型，包括 GPT-3.5-Turbo、GPT-4 等。

**特点**：
- 高质量的回应生成
- 强大的上下文理解能力
- 支持函数调用
- 完善的内容安全过滤

### Cloudflare 适配器

连接 Cloudflare Workers AI 提供的模型服务。

**特点**：
- 低延迟
- 全球分布式部署
- 稳定的连接性
- 较低的使用成本

### Ollama 适配器

连接本地部署的 Ollama 服务，支持多种开源模型。

**特点**：
- 本地部署，无需外部 API
- 完全控制数据隐私
- 支持多种开源模型
- 无使用配额限制

### Azure OpenAI 适配器

连接 Microsoft Azure 上的 OpenAI 服务。

**特点**：
- 企业级安全性和合规性
- 与 Azure 服务集成
- 稳定的服务水平协议
- 区域数据驻留选项

### Anthropic 适配器

连接 Anthropic 的 Claude 系列模型。

**特点**：
- 优秀的长文本处理能力
- 强调安全和有益的回应
- 独特的对话风格
- 较低的幻觉率

### 自定义适配器

支持连接自定义的语言模型 API。

**特点**：
- 灵活的 API 端点配置
- 自定义请求和响应格式
- 支持私有部署的模型
- 可扩展的参数设置

## 基本配置

多适配器的基本配置包括定义多个 API 连接和设置负载均衡策略：

```yaml
Adapters:
  Enabled: true                   # 是否启用多适配器系统
  DefaultAdapter: "openai"        # 默认适配器
  LoadBalancing:
    Strategy: "weighted_random"   # 负载均衡策略
    FailoverEnabled: true         # 是否启用故障转移
    MaxRetries: 3                 # 最大重试次数
    RetryDelay: 1000              # 重试延迟（毫秒）
  
  AdapterList:
    - Name: "openai-gpt4"
      Type: "openai"
      Enabled: true
      Priority: 10
      Weight: 3
      Config:
        ApiKey: "your_openai_api_key"
        Model: "gpt-4"
        BaseUrl: "https://api.openai.com/v1"
    
    - Name: "openai-gpt35"
      Type: "openai"
      Enabled: true
      Priority: 5
      Weight: 7
      Config:
        ApiKey: "your_openai_api_key"
        Model: "gpt-3.5-turbo"
        BaseUrl: "https://api.openai.com/v1"
    
    - Name: "ollama-local"
      Type: "ollama"
      Enabled: true
      Priority: 1
      Weight: 5
      Config:
        Endpoint: "http://localhost:11434/api"
        Model: "llama2"
```

### 基本参数

| 参数名 | 描述 | 默认值 | 可选值 |
|-------|------|-------|-------|
| `Enabled` | 是否启用多适配器系统 | true | true, false |
| `DefaultAdapter` | 默认适配器名称 | "openai" | 任何已配置的适配器名称 |
| `LoadBalancing.Strategy` | 负载均衡策略 | "weighted_random" | "round_robin", "weighted_random", "priority", "least_used" |
| `LoadBalancing.FailoverEnabled` | 是否启用故障转移 | true | true, false |
| `LoadBalancing.MaxRetries` | 最大重试次数 | 3 | 1-10 |
| `LoadBalancing.RetryDelay` | 重试延迟（毫秒） | 1000 | 100-10000 |

### 适配器参数

每个适配器配置包含以下通用参数：

| 参数名 | 描述 | 默认值 | 可选值 |
|-------|------|-------|-------|
| `Name` | 适配器名称 | - | 唯一标识符 |
| `Type` | 适配器类型 | - | "openai", "cloudflare", "ollama", "azure", "anthropic", "custom" |
| `Enabled` | 是否启用该适配器 | true | true, false |
| `Priority` | 适配器优先级（数字越大优先级越高） | 5 | 1-100 |
| `Weight` | 负载均衡权重 | 1 | 1-100 |
| `Config` | 适配器特定配置 | - | 取决于适配器类型 |

## 负载均衡策略

YesImBot 支持多种负载均衡策略，可以根据需求选择最合适的策略：

### 轮询（Round Robin）

按顺序将请求分配给每个可用的适配器。

**适用场景**：
- 适配器性能相似
- 请求负载均匀
- 简单的负载分配需求

**配置示例**：
```yaml
LoadBalancing:
  Strategy: "round_robin"
```

### 加权随机（Weighted Random）

根据权重随机选择适配器，权重越高被选中的概率越大。

**适用场景**：
- 适配器性能不同
- 需要按比例分配负载
- 灵活的负载分配需求

**配置示例**：
```yaml
LoadBalancing:
  Strategy: "weighted_random"
```

### 优先级（Priority）

按优先级选择适配器，只有当高优先级适配器不可用时才使用低优先级适配器。

**适用场景**：
- 有明确的适配器优先顺序
- 主备架构
- 成本控制需求

**配置示例**：
```yaml
LoadBalancing:
  Strategy: "priority"
```

### 最少使用（Least Used）

选择当前负载最小的适配器。

**适用场景**：
- 适配器性能可能波动
- 动态负载平衡需求
- 避免单点过载

**配置示例**：
```yaml
LoadBalancing:
  Strategy: "least_used"
```

## 故障转移配置

故障转移功能允许系统在适配器失败时自动切换到备用适配器，提高系统可靠性：

```yaml
LoadBalancing:
  FailoverEnabled: true
  MaxRetries: 3
  RetryDelay: 1000
  FailureDetection:
    Timeout: 10000              # 请求超时时间（毫秒）
    ErrorThreshold: 5           # 错误阈值
    HealthCheckInterval: 60000  # 健康检查间隔（毫秒）
  Recovery:
    AutoRecovery: true          # 是否自动恢复
    RecoveryDelay: 300000       # 恢复延迟（毫秒）
    GradualRecovery: true       # 是否渐进恢复
```

### 故障转移参数

| 参数名 | 描述 | 默认值 | 推荐范围 |
|-------|------|-------|---------|
| `FailoverEnabled` | 是否启用故障转移 | true | - |
| `MaxRetries` | 最大重试次数 | 3 | 1-10 |
| `RetryDelay` | 重试延迟（毫秒） | 1000 | 100-10000 |
| `FailureDetection.Timeout` | 请求超时时间（毫秒） | 10000 | 1000-60000 |
| `FailureDetection.ErrorThreshold` | 错误阈值 | 5 | 1-20 |
| `FailureDetection.HealthCheckInterval` | 健康检查间隔（毫秒） | 60000 | 10000-600000 |
| `Recovery.AutoRecovery` | 是否自动恢复 | true | - |
| `Recovery.RecoveryDelay` | 恢复延迟（毫秒） | 300000 | 60000-3600000 |
| `Recovery.GradualRecovery` | 是否渐进恢复 | true | - |

## 适配器特定配置

每种适配器类型都有其特定的配置参数：

### OpenAI 适配器配置

```yaml
Config:
  ApiKey: "your_openai_api_key"
  Model: "gpt-4"
  BaseUrl: "https://api.openai.com/v1"
  Organization: ""              # 组织 ID（可选）
  MaxTokens: 1000               # 最大令牌数
  Temperature: 0.7              # 温度参数
  TopP: 0.9                     # Top-p 采样参数
  FrequencyPenalty: 0.5         # 频率惩罚参数
  PresencePenalty: 0.5          # 存在惩罚参数
  Timeout: 60000                # 请求超时时间（毫秒）
  Proxy: ""                     # 代理设置（可选）
```

### Ollama 适配器配置

```yaml
Config:
  Endpoint: "http://localhost:11434/api"
  Model: "llama2"
  MaxTokens: 1000
  Temperature: 0.7
  TopP: 0.9
  RepeatPenalty: 1.1
  Timeout: 60000
```

### Azure OpenAI 适配器配置

```yaml
Config:
  ApiKey: "your_azure_api_key"
  Endpoint: "https://your-resource.openai.azure.com"
  DeploymentName: "your-deployment"
  ApiVersion: "2023-05-15"
  MaxTokens: 1000
  Temperature: 0.7
  TopP: 0.9
  FrequencyPenalty: 0.5
  PresencePenalty: 0.5
  Timeout: 60000
```

### Anthropic 适配器配置

```yaml
Config:
  ApiKey: "your_anthropic_api_key"
  Model: "claude-2"
  MaxTokens: 1000
  Temperature: 0.7
  TopP: 0.9
  TopK: 40
  Timeout: 60000
```

### 自定义适配器配置

```yaml
Config:
  Endpoint: "https://your-custom-api.com/v1/chat"
  ApiKey: "your_api_key"
  Headers:
    "Content-Type": "application/json"
    "Authorization": "Bearer your_api_key"
  RequestTemplate: |
    {
      "model": "{{model}}",
      "messages": {{messages}},
      "temperature": {{temperature}},
      "max_tokens": {{max_tokens}}
    }
  ResponseMapping:
    ContentPath: "choices[0].message.content"
    ErrorPath: "error.message"
  Timeout: 60000
```

## 模型选择策略

YesImBot 支持根据任务需求动态选择最合适的模型：

```yaml
ModelSelection:
  Enabled: true
  Strategy: "task_based"        # 模型选择策略
  TaskRules:
    - TaskType: "creative"
      PreferredModels:
        - "openai-gpt4"
        - "anthropic-claude"
    - TaskType: "factual"
      PreferredModels:
        - "openai-gpt4"
        - "openai-gpt35"
    - TaskType: "coding"
      PreferredModels:
        - "openai-gpt4"
        - "ollama-codellama"
  DefaultTask: "general"
```

### 模型选择参数

| 参数名 | 描述 | 默认值 | 可选值 |
|-------|------|-------|-------|
| `Enabled` | 是否启用模型选择 | true | true, false |
| `Strategy` | 模型选择策略 | "task_based" | "task_based", "cost_based", "performance_based", "hybrid" |
| `TaskRules` | 任务规则列表 | - | 任务类型和首选模型的映射 |
| `DefaultTask` | 默认任务类型 | "general" | 任何已定义的任务类型 |

## 监控和日志

多适配器系统提供了详细的监控和日志功能，帮助您了解系统运行状况：

```yaml
Monitoring:
  Enabled: true
  LogLevel: "info"              # 日志级别
  MetricsCollection: true       # 是否收集指标
  PerformanceTracking: true     # 是否跟踪性能
  AlertThresholds:
    ErrorRate: 0.1              # 错误率阈值
    LatencyMs: 5000             # 延迟阈值（毫秒）
    FailoverCount: 10           # 故障转移计数阈值
```

### 监控参数

| 参数名 | 描述 | 默认值 | 可选值 |
|-------|------|-------|-------|
| `Enabled` | 是否启用监控 | true | true, false |
| `LogLevel` | 日志级别 | "info" | "debug", "info", "warn", "error" |
| `MetricsCollection` | 是否收集指标 | true | true, false |
| `PerformanceTracking` | 是否跟踪性能 | true | true, false |
| `AlertThresholds.ErrorRate` | 错误率阈值 | 0.1 | 0.01-0.5 |
| `AlertThresholds.LatencyMs` | 延迟阈值（毫秒） | 5000 | 1000-60000 |
| `AlertThresholds.FailoverCount` | 故障转移计数阈值 | 10 | 1-100 |

## 配置示例

以下是一个完整的多适配器配置示例：

```yaml
Adapters:
  Enabled: true
  DefaultAdapter: "openai-gpt35"
  
  LoadBalancing:
    Strategy: "weighted_random"
    FailoverEnabled: true
    MaxRetries: 3
    RetryDelay: 1000
    FailureDetection:
      Timeout: 10000
      ErrorThreshold: 5
      HealthCheckInterval: 60000
    Recovery:
      AutoRecovery: true
      RecoveryDelay: 300000
      GradualRecovery: true
  
  ModelSelection:
    Enabled: true
    Strategy: "task_based"
    TaskRules:
      - TaskType: "creative"
        PreferredModels:
          - "openai-gpt4"
          - "anthropic-claude"
      - TaskType: "factual"
        PreferredModels:
          - "openai-gpt4"
          - "openai-gpt35"
      - TaskType: "coding"
        PreferredModels:
          - "openai-gpt4"
          - "ollama-codellama"
    DefaultTask: "general"
  
  Monitoring:
    Enabled: true
    LogLevel: "info"
    MetricsCollection: true
    PerformanceTracking: true
    AlertThresholds:
      ErrorRate: 0.1
      LatencyMs: 5000
      FailoverCount: 10
  
  AdapterList:
    - Name: "openai-gpt4"
      Type: "openai"
      Enabled: true
      Priority: 10
      Weight: 3
      Config:
        ApiKey: "your_openai_api_key"
        Model: "gpt-4"
        BaseUrl: "https://api.openai.com/v1"
        MaxTokens: 1000
        Temperature: 0.7
        TopP: 0.9
        FrequencyPenalty: 0.5
        PresencePenalty: 0.5
        Timeout: 60000
    
    - Name: "openai-gpt35"
      Type: "openai"
      Enabled: true
      Priority: 5
      Weight: 7
      Config:
        ApiKey: "your_openai_api_key"
        Model: "gpt-3.5-turbo"
        BaseUrl: "https://api.openai.com/v1"
        MaxTokens: 1000
        Temperature: 0.7
        TopP: 0.9
        FrequencyPenalty: 0.5
        PresencePenalty: 0.5
        Timeout: 60000
    
    - Name: "ollama-llama2"
      Type: "ollama"
      Enabled: true
      Priority: 1
      Weight: 5
      Config:
        Endpoint: "http://localhost:11434/api"
        Model: "llama2"
        MaxTokens: 1000
        Temperature: 0.7
        TopP: 0.9
        RepeatPenalty: 1.1
        Timeout: 60000
    
    - Name: "ollama-codellama"
      Type: "ollama"
      Enabled: true
      Priority: 1
      Weight: 2
      Config:
        Endpoint: "http://localhost:11434/api"
        Model: "codellama"
        MaxTokens: 1000
        Temperature: 0.5
        TopP: 0.95
        RepeatPenalty: 1.1
        Timeout: 60000
    
    - Name: "anthropic-claude"
      Type: "anthropic"
      Enabled: true
      Priority: 8
      Weight: 3
      Config:
        ApiKey: "your_anthropic_api_key"
        Model: "claude-2"
        MaxTokens: 1000
        Temperature: 0.7
        TopP: 0.9
        TopK: 40
        Timeout: 60000
```

## 最佳实践

### 适配器选择

1. **多样化适配器**：配置不同类型的适配器，提高系统可靠性
2. **本地备份**：包含至少一个本地适配器（如 Ollama），作为外部 API 不可用时的备份
3. **性能平衡**：根据性能和成本平衡使用不同的适配器
4. **任务匹配**：为不同任务选择最合适的适配器

### 负载均衡

1. **合理权重**：根据适配器的性能和成本设置合理的权重
2. **动态调整**：定期评估和调整负载均衡策略
3. **峰值处理**：为处理峰值负载配置足够的适配器
4. **成本控制**：使用优先级策略控制高成本适配器的使用

### 故障转移

1. **快速检测**：设置合理的超时和错误阈值，快速检测故障
2. **渐进恢复**：启用渐进恢复，避免恢复后立即过载
3. **定期测试**：定期测试故障转移功能，确保其正常工作
4. **监控警报**：设置监控和警报，及时发现故障

### 性能优化

1. **缓存结果**：对于常见查询，缓存结果减少 API 调用
2. **批处理请求**：合并多个请求，减少 API 调用次数
3. **预热连接**：定期发送小型请求，保持连接活跃
4. **异步处理**：使用异步处理，避免阻塞主线程

## 常见问题

### 适配器连接失败

如果适配器连接失败，可能是因为：

1. **API 密钥错误**：检查 API 密钥是否正确
2. **网络问题**：检查网络连接，特别是对于外部 API
3. **端点错误**：验证 API 端点 URL 是否正确
4. **配额限制**：检查是否达到了 API 使用配额

解决方案：
- 验证 API 密钥和端点
- 使用代理或 VPN 解决网络问题
- 增加超时时间
- 联系 API 提供商解决配额问题

### 负载均衡不均匀

如果负载分配不均匀，可能是因为：

1. **权重设置不合理**：检查适配器权重设置
2. **策略选择不当**：当前策略可能不适合您的需求
3. **适配器性能差异大**：性能差异导致负载不均衡
4. **故障转移频繁**：频繁的故障转移影响负载分配

解决方案：
- 调整适配器权重
- 更换负载均衡策略
- 优化性能较差的适配器
- 解决导致故障转移的问题

### 响应质量不一致

如果不同适配器的响应质量不一致，可能是因为：

1. **模型能力差异**：不同模型的能力和训练数据不同
2. **参数设置不同**：温度、Top-P 等参数设置不同
3. **提示词兼容性**：提示词可能对某些模型更有效
4. **上下文处理差异**：不同模型处理上下文的方式不同

解决方案：
- 为每个适配器优化模型参数
- 调整提示词以适应不同模型
- 使用模型选择策略，为不同任务选择合适的模型
- 为高质量要求的任务指定特定适配器

### 性能问题

如果遇到性能问题，可能是因为：

1. **适配器过载**：某些适配器可能过载
2. **网络延迟**：外部 API 的网络延迟
3. **资源限制**：本地适配器的资源限制
4. **配置不当**：超时、重试等配置不当

解决方案：
- 增加适配器数量或调整权重
- 使用地理位置更近的 API 端点
- 增加本地适配器的资源分配
- 优化超时和重试配置
