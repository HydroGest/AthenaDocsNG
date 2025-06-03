# API 配置

API 配置是 YesImBot 的核心设置之一，它决定了机器人使用哪些语言模型 API 来生成回复。YesImBot 支持多种 API 类型，并提供负载均衡和故障转移功能，确保机器人能够稳定运行。

## API 配置概述

YesImBot 的 API 配置主要包括以下内容：

1. **API 列表**：定义可用的语言模型 API
2. **负载均衡**：控制多个 API 之间的请求分配
3. **故障转移**：当某个 API 失败时自动切换到备用 API
4. **模型参数**：调整语言模型的生成行为

## API 列表配置

API 列表是 YesImBot 可以使用的语言模型 API 集合。您可以配置一个或多个 API，YesImBot 将根据负载均衡和故障转移策略选择合适的 API 发送请求。

```yaml
API:
  APIList:
    - APIType: "OpenAI"
      BaseURL: "https://api.openai.com"
      APIKey: "your_openai_api_key"
      AIModel: "gpt-4o-mini"
      Priority: 10
    - APIType: "Cloudflare"
      BaseURL: "https://api.cloudflare.com/client/v4"
      APIKey: "your_cloudflare_api_key"
      AIModel: "@cf/meta/llama-3-8b-instruct"
      UID: "your_cloudflare_account_id"
      Priority: 5
    - APIType: "Ollama"
      BaseURL: "http://localhost:11434"
      AIModel: "llama2"
      Priority: 1
```

### 支持的 API 类型

YesImBot 目前支持以下 API 类型：

1. **OpenAI**：OpenAI 的 API，支持 GPT-3.5、GPT-4 等模型
2. **Cloudflare**：Cloudflare 的 AI API，支持多种开源模型
3. **Ollama**：本地部署的 Ollama 服务，支持多种开源模型

### 通用参数

所有 API 类型都需要配置以下通用参数：

| 参数名 | 描述 | 示例 |
|-------|------|------|
| `APIType` | API 类型 | `"OpenAI"`, `"Cloudflare"`, `"Ollama"` |
| `BaseURL` | API 的基础 URL | `"https://api.openai.com"` |
| `AIModel` | 使用的模型名称 | `"gpt-4o-mini"` |
| `Priority` | API 的优先级，数值越大优先级越高 | `10` |

### OpenAI 特有参数

| 参数名 | 描述 | 示例 |
|-------|------|------|
| `APIKey` | OpenAI API 密钥 | `"sk-..."` |
| `Organization` | （可选）OpenAI 组织 ID | `"org-..."` |

### Cloudflare 特有参数

| 参数名 | 描述 | 示例 |
|-------|------|------|
| `APIKey` | Cloudflare API 密钥 | `"your_api_key"` |
| `UID` | Cloudflare 账户 ID | `"your_account_id"` |

### Ollama 特有参数

Ollama 没有特有参数，只需配置通用参数即可。

## 负载均衡配置

负载均衡控制多个 API 之间的请求分配，可以根据优先级、权重或轮询方式进行分配。

```yaml
API:
  LoadBalancing:
    Strategy: "priority"  # 负载均衡策略：priority, weighted, round-robin
    HealthCheck: true     # 是否启用健康检查
    CheckInterval: 300    # 健康检查间隔（秒）
```

### 负载均衡策略

YesImBot 支持以下负载均衡策略：

1. **priority**：按优先级使用 API，只有当高优先级 API 不可用时才使用低优先级 API
2. **weighted**：根据 API 的优先级作为权重，按比例分配请求
3. **round-robin**：轮询方式，依次使用每个 API

### 健康检查

健康检查定期测试每个 API 的可用性，确保只将请求发送到正常工作的 API。

| 参数名 | 描述 | 默认值 | 推荐范围 |
|-------|------|-------|---------|
| `HealthCheck` | 是否启用健康检查 | `true` | - |
| `CheckInterval` | 健康检查间隔（秒） | `300` | 60-600 |
| `FailureThreshold` | 标记 API 不可用的连续失败次数 | `3` | 2-5 |
| `SuccessThreshold` | 标记 API 恢复可用的连续成功次数 | `2` | 1-3 |

## 故障转移配置

故障转移功能在当前 API 请求失败时，自动切换到备用 API，确保机器人能够持续运行。

```yaml
API:
  FailOver:
    Enabled: true               # 是否启用故障转移
    MaxRetries: 3               # 最大重试次数
    RetryDelay: 1000            # 重试延迟（毫秒）
    ExponentialBackoff: true    # 是否使用指数退避策略
```

### 故障转移参数

| 参数名 | 描述 | 默认值 | 推荐范围 |
|-------|------|-------|---------|
| `Enabled` | 是否启用故障转移 | `true` | - |
| `MaxRetries` | 最大重试次数 | `3` | 1-5 |
| `RetryDelay` | 重试延迟（毫秒） | `1000` | 500-5000 |
| `ExponentialBackoff` | 是否使用指数退避策略 | `true` | - |

## 模型参数配置

模型参数控制语言模型生成回复的行为，如温度、最大令牌数等。

```yaml
API:
  ModelParameters:
    Temperature: 0.7            # 温度参数，控制随机性
    MaxTokens: 1000             # 最大生成令牌数
    TopP: 0.9                   # Top-p 采样参数
    FrequencyPenalty: 0.5       # 频率惩罚参数
    PresencePenalty: 0.5        # 存在惩罚参数
```

### 模型参数说明

| 参数名 | 描述 | 默认值 | 推荐范围 |
|-------|------|-------|---------|
| `Temperature` | 控制生成文本的随机性，值越高随机性越大 | `0.7` | 0.1-1.0 |
| `MaxTokens` | 单次生成的最大令牌数 | `1000` | 100-4000 |
| `TopP` | 控制生成文本的多样性，值越低越保守 | `0.9` | 0.1-1.0 |
| `FrequencyPenalty` | 降低模型重复使用相同词语的倾向 | `0.5` | 0.0-2.0 |
| `PresencePenalty` | 降低模型重复讨论相同主题的倾向 | `0.5` | 0.0-2.0 |

## 完整配置示例

以下是一个完整的 API 配置示例，包含多种 API 类型和所有配置选项：

```yaml
API:
  APIList:
    - APIType: "OpenAI"
      BaseURL: "https://api.openai.com"
      APIKey: "your_openai_api_key"
      AIModel: "gpt-4o-mini"
      Priority: 10
    - APIType: "Cloudflare"
      BaseURL: "https://api.cloudflare.com/client/v4"
      APIKey: "your_cloudflare_api_key"
      AIModel: "@cf/meta/llama-3-8b-instruct"
      UID: "your_cloudflare_account_id"
      Priority: 5
    - APIType: "Ollama"
      BaseURL: "http://localhost:11434"
      AIModel: "llama2"
      Priority: 1
  
  LoadBalancing:
    Strategy: "priority"
    HealthCheck: true
    CheckInterval: 300
    FailureThreshold: 3
    SuccessThreshold: 2
  
  FailOver:
    Enabled: true
    MaxRetries: 3
    RetryDelay: 1000
    ExponentialBackoff: true
  
  ModelParameters:
    Temperature: 0.7
    MaxTokens: 1000
    TopP: 0.9
    FrequencyPenalty: 0.5
    PresencePenalty: 0.5
```

## 最佳实践

1. **多 API 配置**：配置多个不同类型的 API，提高系统的可靠性和灵活性
2. **优先级设置**：将性能更好的 API 设置为更高优先级，将本地 API 设置为最低优先级作为备用
3. **模型选择**：根据需求选择合适的模型，对于一般对话可以使用较小模型，对于复杂任务使用更强大的模型
4. **参数调优**：根据实际使用情况调整模型参数，找到随机性和一致性之间的平衡
5. **定期更新**：关注各 API 提供商的更新，及时更新模型名称和参数

## 常见问题

### API 连接失败

如果 API 连接失败，可能是因为：

1. API 密钥不正确或已过期
2. 基础 URL 不正确
3. 网络连接问题
4. API 服务不可用

解决方案：
- 检查 API 密钥和基础 URL 是否正确
- 确认网络连接正常
- 检查 API 服务状态
- 配置多个备用 API 启用故障转移

### 模型生成质量不佳

如果模型生成的回复质量不佳，可能是因为：

1. 模型参数设置不合适
2. 选择的模型能力有限
3. 提示词设计不佳

解决方案：
- 调整模型参数，如降低温度使回复更加确定性
- 尝试使用更强大的模型
- 优化提示词设计

### 响应速度慢

如果 API 响应速度慢，可能是因为：

1. 选择的模型处理速度慢
2. API 服务负载高
3. 网络延迟大

解决方案：
- 使用响应更快的模型或 API
- 优化提示词，减少生成的令牌数
- 调整 `MaxTokens` 参数限制生成长度
