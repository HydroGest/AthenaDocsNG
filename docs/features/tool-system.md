# 工具系统

YesImBot 的工具系统是一个强大的扩展功能，允许机器人执行各种操作，如搜索信息、生成图像、查询数据等。本章将详细介绍工具系统的工作原理、配置方法和使用方式，帮助您充分利用这一功能增强机器人的能力。

## 工具系统概述

工具系统基于函数调用框架，使机器人能够识别用户的意图，并执行相应的工具操作。这大大扩展了机器人的功能范围，使其不仅能够进行对话，还能执行实际的任务和操作。

工具系统的主要特点包括：

1. **多种内置工具**：提供一系列常用工具，如搜索、计算器、天气查询等
2. **自定义工具支持**：允许用户开发和添加自定义工具
3. **权限控制**：精细的工具使用权限控制
4. **上下文集成**：工具执行结果自动集成到对话上下文中
5. **多模态支持**：支持文本、图像等多种输入和输出格式

## 内置工具

YesImBot 提供了多种内置工具，可以直接使用：

### 搜索工具

允许机器人搜索网络信息，获取最新数据。

```yaml
Tools:
  Search:
    Enabled: true
    Provider: "google"           # 搜索提供商：google, bing, baidu
    ApiKey: "your_api_key"       # API 密钥
    SafeSearch: "moderate"       # 安全搜索级别：off, moderate, strict
    MaxResults: 5                # 最大结果数
    Timeout: 10000               # 超时时间（毫秒）
```

### 计算器工具

允许机器人执行数学计算。

```yaml
Tools:
  Calculator:
    Enabled: true
    MaxDigits: 10                # 最大位数
    AllowFunctions: true         # 是否允许函数计算
    AllowedFunctions:            # 允许的函数列表
      - "sin"
      - "cos"
      - "tan"
      - "sqrt"
      - "log"
```

### 天气工具

允许机器人查询天气信息。

```yaml
Tools:
  Weather:
    Enabled: true
    Provider: "openweathermap"   # 天气提供商
    ApiKey: "your_api_key"       # API 密钥
    Units: "metric"              # 单位：metric, imperial
    Language: "zh_cn"            # 语言
    DefaultLocation: "Beijing"   # 默认位置
```

### 图像生成工具

允许机器人生成图像。

```yaml
Tools:
  ImageGeneration:
    Enabled: true
    Provider: "openai"           # 图像生成提供商：openai, stability
    ApiKey: "your_api_key"       # API 密钥
    Model: "dall-e-3"            # 模型
    Size: "1024x1024"            # 图像大小
    Quality: "standard"          # 质量：standard, hd
    Style: "vivid"               # 风格：vivid, natural
    MaxGenerationsPerDay: 50     # 每日最大生成次数
```

### 翻译工具

允许机器人翻译文本。

```yaml
Tools:
  Translation:
    Enabled: true
    Provider: "google"           # 翻译提供商：google, baidu, microsoft
    ApiKey: "your_api_key"       # API 密钥
    DefaultSourceLanguage: "auto" # 默认源语言
    DefaultTargetLanguage: "zh_cn" # 默认目标语言
    SupportedLanguages:          # 支持的语言列表
      - "en"
      - "zh_cn"
      - "ja"
      - "ko"
      - "fr"
      - "de"
```

### 知识库工具

允许机器人查询自定义知识库。

```yaml
Tools:
  KnowledgeBase:
    Enabled: true
    Type: "vector"               # 知识库类型：vector, keyword
    VectorStore: "qdrant"        # 向量存储：qdrant, pinecone, milvus
    Endpoint: "http://localhost:6333" # 端点
    Collection: "knowledge"      # 集合名称
    EmbeddingModel: "openai"     # 嵌入模型
    ApiKey: "your_api_key"       # API 密钥
    MaxResults: 5                # 最大结果数
    ScoreThreshold: 0.7          # 分数阈值
```

## 工具配置

工具系统的基本配置包括全局设置和各个工具的具体配置：

```yaml
ToolSystem:
  Enabled: true                  # 是否启用工具系统
  AutoToolSelection: true        # 是否自动选择工具
  MaxToolsPerRequest: 3          # 每个请求最多使用的工具数
  ToolExecutionTimeout: 30000    # 工具执行超时时间（毫秒）
  IncludeToolResultsInContext: true # 是否在上下文中包含工具结果
  
  Permissions:
    AdminOnly: false             # 是否仅管理员可用
    AllowedUsers:                # 允许使用工具的用户列表
      - "123456789"
    AllowedGroups:               # 允许使用工具的群组列表
      - "987654321"
    BlockedUsers:                # 禁止使用工具的用户列表
      - "111222333"
    BlockedGroups:               # 禁止使用工具的群组列表
      - "444555666"
  
  Tools:
    # 各个工具的具体配置
    # ...
```

### 基本参数

| 参数名 | 描述 | 默认值 | 可选值 |
|-------|------|-------|-------|
| `Enabled` | 是否启用工具系统 | true | true, false |
| `AutoToolSelection` | 是否自动选择工具 | true | true, false |
| `MaxToolsPerRequest` | 每个请求最多使用的工具数 | 3 | 1-10 |
| `ToolExecutionTimeout` | 工具执行超时时间（毫秒） | 30000 | 1000-300000 |
| `IncludeToolResultsInContext` | 是否在上下文中包含工具结果 | true | true, false |

### 权限参数

| 参数名 | 描述 | 默认值 | 可选值 |
|-------|------|-------|-------|
| `Permissions.AdminOnly` | 是否仅管理员可用 | false | true, false |
| `Permissions.AllowedUsers` | 允许使用工具的用户列表 | [] | 用户 ID 数组 |
| `Permissions.AllowedGroups` | 允许使用工具的群组列表 | [] | 群组 ID 数组 |
| `Permissions.BlockedUsers` | 禁止使用工具的用户列表 | [] | 用户 ID 数组 |
| `Permissions.BlockedGroups` | 禁止使用工具的群组列表 | [] | 群组 ID 数组 |

## 工具使用方式

YesImBot 的工具可以通过以下方式使用：

### 自动工具调用

当启用 `AutoToolSelection` 时，机器人会自动识别用户的意图，并选择合适的工具执行。用户只需正常提问，无需特殊命令。

例如，用户可以直接问：
- "今天北京的天气怎么样？"
- "计算 (15 * 27) / 3 的结果"
- "搜索最新的 AI 研究进展"

机器人会自动识别意图，选择合适的工具（天气工具、计算器工具或搜索工具），执行操作，并返回结果。

### 显式工具调用

用户也可以通过特定命令显式调用工具：

```
@机器人 使用搜索工具 最新的 AI 研究进展
@机器人 使用天气工具 查询北京的天气
@机器人 使用计算器 (15 * 27) / 3
```

显式调用可以确保机器人使用特定的工具，避免意图识别错误。

### 工具链

工具系统支持工具链，允许多个工具按顺序执行，前一个工具的输出作为后一个工具的输入：

```
@机器人 使用搜索工具查找最新的 AI 论文，然后使用翻译工具将结果翻译成中文
```

工具链可以实现更复杂的任务，提高机器人的能力。

## 自定义工具开发

YesImBot 允许用户开发和添加自定义工具，扩展机器人的功能：

### 工具定义

自定义工具需要定义以下组件：

1. **工具名称**：唯一标识符
2. **工具描述**：工具的功能描述
3. **参数定义**：工具接受的参数
4. **执行函数**：工具的实际执行逻辑
5. **结果处理**：处理和格式化工具执行结果

### JavaScript 工具示例

```javascript
// custom-tool.js
module.exports = {
  name: "CustomTool",
  description: "这是一个自定义工具示例",
  parameters: {
    query: {
      type: "string",
      description: "查询参数"
    },
    limit: {
      type: "number",
      description: "结果数量限制",
      default: 5
    }
  },
  
  async execute(params) {
    const { query, limit } = params;
    
    // 工具执行逻辑
    const results = await someFunction(query, limit);
    
    // 返回结果
    return {
      success: true,
      data: results,
      message: `查询 "${query}" 的结果`
    };
  }
};
```

### Python 工具示例

```python
# custom_tool.py
def execute(params):
    query = params.get("query")
    limit = params.get("limit", 5)
    
    # 工具执行逻辑
    results = some_function(query, limit)
    
    # 返回结果
    return {
        "success": True,
        "data": results,
        "message": f'查询 "{query}" 的结果'
    }

# 工具定义
tool_definition = {
    "name": "CustomTool",
    "description": "这是一个自定义工具示例",
    "parameters": {
        "query": {
            "type": "string",
            "description": "查询参数"
        },
        "limit": {
            "type": "number",
            "description": "结果数量限制",
            "default": 5
        }
    },
    "execute": execute
}
```

### 工具注册

自定义工具需要在配置中注册才能使用：

```yaml
ToolSystem:
  # 其他配置...
  
  CustomTools:
    - Name: "CustomTool"
      Type: "js"                 # 工具类型：js, python
      Path: "/path/to/custom-tool.js" # 工具脚本路径
      Enabled: true
      RequireAdmin: false        # 是否需要管理员权限
      Timeout: 10000             # 超时时间（毫秒）
```

## 工具执行流程

工具的执行流程包括以下步骤：

1. **意图识别**：识别用户的意图，确定是否需要使用工具
2. **工具选择**：选择合适的工具执行
3. **参数提取**：从用户消息中提取工具所需的参数
4. **权限检查**：检查用户是否有权限使用该工具
5. **工具执行**：执行工具操作
6. **结果处理**：处理工具执行结果
7. **结果集成**：将结果集成到对话上下文中
8. **回应生成**：生成包含工具执行结果的回应

## 工具结果处理

工具执行结果可以以多种方式处理和呈现：

### 文本结果

最基本的结果形式，直接作为文本返回：

```
搜索结果：
1. 标题：最新 AI 研究进展
   链接：https://example.com/ai-research
   摘要：这篇文章介绍了最新的 AI 研究进展...

2. 标题：人工智能的未来发展
   链接：https://example.com/ai-future
   摘要：专家预测人工智能的未来发展趋势...
```

### 结构化数据

结构化数据可以更清晰地呈现信息：

```json
{
  "weather": {
    "location": "北京",
    "temperature": 25,
    "condition": "晴",
    "humidity": 40,
    "wind": "东北风 3级"
  }
}
```

### 多模态结果

工具可以返回多模态结果，如图像、音频等：

```
[图像：生成的猫咪图片]

这是根据您的描述生成的猫咪图片。这只橙色的猫咪正坐在窗台上，望着窗外的雨景。
```

## 工具系统优化

为了获得最佳的工具系统性能，可以考虑以下优化：

### 性能优化

1. **缓存结果**：对于频繁使用的工具查询，缓存结果以减少重复执行
2. **并行执行**：允许多个工具并行执行，减少等待时间
3. **超时控制**：设置合理的超时时间，避免工具执行时间过长
4. **资源限制**：限制工具的资源使用，避免单个工具占用过多资源

### 用户体验优化

1. **进度反馈**：对于长时间运行的工具，提供执行进度反馈
2. **错误处理**：优雅地处理工具执行错误，提供有用的错误信息
3. **结果格式化**：根据结果类型选择最合适的呈现方式
4. **上下文保留**：在对话中保留工具执行结果的上下文，便于后续引用

## 配置示例

以下是一个完整的工具系统配置示例：

```yaml
ToolSystem:
  Enabled: true
  AutoToolSelection: true
  MaxToolsPerRequest: 3
  ToolExecutionTimeout: 30000
  IncludeToolResultsInContext: true
  
  Permissions:
    AdminOnly: false
    AllowedUsers:
      - "123456789"
    AllowedGroups:
      - "987654321"
    BlockedUsers:
      - "111222333"
    BlockedGroups:
      - "444555666"
  
  Tools:
    Search:
      Enabled: true
      Provider: "google"
      ApiKey: "your_google_api_key"
      SafeSearch: "moderate"
      MaxResults: 5
      Timeout: 10000
    
    Calculator:
      Enabled: true
      MaxDigits: 10
      AllowFunctions: true
      AllowedFunctions:
        - "sin"
        - "cos"
        - "tan"
        - "sqrt"
        - "log"
    
    Weather:
      Enabled: true
      Provider: "openweathermap"
      ApiKey: "your_openweathermap_api_key"
      Units: "metric"
      Language: "zh_cn"
      DefaultLocation: "Beijing"
    
    ImageGeneration:
      Enabled: true
      Provider: "openai"
      ApiKey: "your_openai_api_key"
      Model: "dall-e-3"
      Size: "1024x1024"
      Quality: "standard"
      Style: "vivid"
      MaxGenerationsPerDay: 50
    
    Translation:
      Enabled: true
      Provider: "google"
      ApiKey: "your_google_translate_api_key"
      DefaultSourceLanguage: "auto"
      DefaultTargetLanguage: "zh_cn"
      SupportedLanguages:
        - "en"
        - "zh_cn"
        - "ja"
        - "ko"
        - "fr"
        - "de"
    
    KnowledgeBase:
      Enabled: true
      Type: "vector"
      VectorStore: "qdrant"
      Endpoint: "http://localhost:6333"
      Collection: "knowledge"
      EmbeddingModel: "openai"
      ApiKey: "your_openai_api_key"
      MaxResults: 5
      ScoreThreshold: 0.7
  
  CustomTools:
    - Name: "CustomTool"
      Type: "js"
      Path: "/path/to/custom-tool.js"
      Enabled: true
      RequireAdmin: false
      Timeout: 10000
```

## 最佳实践

1. **选择性启用工具**：只启用实际需要的工具，减少资源消耗
2. **设置合理的权限**：根据用户和群组的需求设置合理的工具使用权限
3. **优化工具参数**：根据实际使用情况优化工具参数，如搜索结果数量、超时时间等
4. **监控工具使用**：定期检查工具使用情况，识别潜在问题
5. **更新 API 密钥**：定期更新工具使用的 API 密钥，确保安全性
6. **测试自定义工具**：在部署前充分测试自定义工具，确保其稳定性和安全性

## 常见问题

### 工具不执行

如果工具不执行，可能是因为：

1. **工具系统未启用**：检查 `ToolSystem.Enabled` 是否设置为 true
2. **特定工具未启用**：检查特定工具的 `Enabled` 是否设置为 true
3. **权限问题**：检查用户是否有权限使用该工具
4. **意图识别失败**：尝试使用显式工具调用

### API 密钥问题

如果遇到 API 密钥问题，可能是因为：

1. **密钥错误**：检查 API 密钥是否正确
2. **密钥过期**：检查 API 密钥是否过期
3. **使用限制**：检查是否达到了 API 使用限制
4. **权限不足**：检查 API 密钥是否有足够的权限

### 工具执行超时

如果工具执行超时，可能是因为：

1. **超时设置过短**：增加 `ToolExecutionTimeout` 值
2. **工具执行时间长**：优化工具执行逻辑，减少执行时间
3. **网络问题**：检查网络连接，特别是对于依赖外部 API 的工具
4. **资源不足**：检查系统资源是否足够

### 自定义工具错误

如果自定义工具出现错误，可能是因为：

1. **脚本错误**：检查工具脚本是否有语法或逻辑错误
2. **路径错误**：确认工具脚本路径是否正确
3. **依赖问题**：检查工具依赖的库或服务是否可用
4. **权限问题**：确认脚本有足够的执行权限
