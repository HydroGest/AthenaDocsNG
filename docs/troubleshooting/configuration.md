# 配置问题

在配置和使用 YesImBot 过程中，您可能会遇到各种配置相关的问题。本章将帮助您识别和解决常见的配置问题，确保 YesImBot 能够按照您的预期工作。

## API 配置问题

### API 密钥无效

**症状**：日志中出现 "Invalid API key" 或 "Authentication failed" 错误。

**解决方案**：
1. 检查 API 密钥是否正确：
   ```yaml
   # 在 koishi.yml 中检查
   plugins:
     yesimbot:
       api:
         openai:
           apiKey: "sk-..."  # 确保密钥格式正确
   ```
2. 确认 API 密钥未过期或被撤销：
   - 登录相应的 API 提供商网站（如 OpenAI）
   - 检查 API 密钥状态
   - 必要时生成新的 API 密钥
3. 确保密钥没有多余的空格或特殊字符。

### API 端点错误

**症状**：日志中出现 "Cannot connect to API" 或 "Endpoint not found" 错误。

**解决方案**：
1. 检查 API 端点配置：
   ```yaml
   plugins:
     yesimbot:
       api:
         openai:
           baseUrl: "https://api.openai.com/v1"  # 确保 URL 正确
   ```
2. 如果使用自定义 API 端点，确保端点可访问：
   ```bash
   # 测试端点可访问性
   curl -I https://your-custom-endpoint.com
   ```
3. 如果使用代理，确保代理配置正确：
   ```yaml
   plugins:
     yesimbot:
       api:
         openai:
           proxy: "http://proxy-server:port"
   ```

### API 使用限制

**症状**：日志中出现 "Rate limit exceeded" 或 "Quota exceeded" 错误。

**解决方案**：
1. 检查 API 使用情况：
   - 登录 API 提供商网站
   - 查看使用统计和限制
2. 配置多个 API 密钥进行负载均衡：
   ```yaml
   plugins:
     yesimbot:
       adapters:
         adapterList:
           - name: "openai-1"
             type: "openai"
             config:
               apiKey: "sk-key1"
           - name: "openai-2"
             type: "openai"
             config:
               apiKey: "sk-key2"
   ```
3. 调整请求频率和批量大小：
   ```yaml
   plugins:
     yesimbot:
       parameters:
         performance:
           batchSize: 3  # 减小批处理大小
           parallelRequests: 2  # 减少并行请求数
   ```

## 意愿值系统问题

### 机器人过于活跃

**症状**：机器人回应过于频繁，甚至回应不相关的消息。

**解决方案**：
1. 提高意愿值阈值：
   ```yaml
   plugins:
     yesimbot:
       parameters:
         willingness:
           willingnessThreshold: 0.8  # 提高阈值（默认为 0.7）
   ```
2. 降低基础意愿值：
   ```yaml
   plugins:
     yesimbot:
       parameters:
         willingness:
           baseWillingness: 0.15  # 降低基础值（默认为 0.2）
   ```
3. 增加冷却期：
   ```yaml
   plugins:
     yesimbot:
       parameters:
         willingness:
           cooldownPeriod: 90  # 增加冷却期（默认为 60 秒）
   ```

### 机器人过于沉默

**症状**：机器人几乎不回应消息，即使被直接提问。

**解决方案**：
1. 降低意愿值阈值：
   ```yaml
   plugins:
     yesimbot:
       parameters:
         willingness:
           willingnessThreshold: 0.6  # 降低阈值（默认为 0.7）
   ```
2. 提高基础意愿值：
   ```yaml
   plugins:
     yesimbot:
       parameters:
         willingness:
           baseWillingness: 0.25  # 提高基础值（默认为 0.2）
   ```
3. 增加名字提及和关键词提升值：
   ```yaml
   plugins:
     yesimbot:
       parameters:
         willingness:
           nameMentionBoost: 0.6  # 提高名字提及提升值（默认为 0.5）
           keywordBoost: 0.4  # 提高关键词提升值（默认为 0.3）
   ```
4. 减少冷却期：
   ```yaml
   plugins:
     yesimbot:
       parameters:
         willingness:
           cooldownPeriod: 30  # 减少冷却期（默认为 60 秒）
   ```

### 意愿值计算不准确

**症状**：机器人的回应模式不符合预期，意愿值计算似乎不准确。

**解决方案**：
1. 启用详细日志，观察意愿值计算：
   ```yaml
   plugins:
     yesimbot:
       debug:
         logLevel: "verbose"
         logWillingnessCalculation: true
   ```
2. 检查关键词配置：
   ```yaml
   plugins:
     yesimbot:
       willingness:
         keywords:
           - "AI"
           - "机器人"
           - "问题"
   ```
3. 确保机器人名称和别名配置正确：
   ```yaml
   plugins:
     yesimbot:
       personality:
         botName: "Athena"
         aliases:
           - "雅典娜"
           - "小雅"
   ```

## 记忆系统问题

### 记忆丢失

**症状**：机器人无法记住之前的对话内容，每次对话都像是新开始。

**解决方案**：
1. 检查记忆槽位配置：
   ```yaml
   plugins:
     yesimbot:
       memory:
         slots:
           - name: "default"
             sessions:
               - "group:123456789"  # 确保包含正确的会话 ID
             capacity: 4000  # 确保容量足够
   ```
2. 增加记忆生命周期：
   ```yaml
   plugins:
     yesimbot:
       parameters:
         memory:
           memoryLifespan: 48  # 增加生命周期（小时，默认为 24）
   ```
3. 降低记忆重要性阈值：
   ```yaml
   plugins:
     yesimbot:
       parameters:
         memory:
           importanceThreshold: 0.4  # 降低阈值（默认为 0.5）
   ```

### 记忆混淆

**症状**：机器人混淆不同对话的内容，或将不相关的信息混合在一起。

**解决方案**：
1. 为不同的对话创建独立的记忆槽位：
   ```yaml
   plugins:
     yesimbot:
       memory:
         slots:
           - name: "group1"
             sessions:
               - "group:123456789"
           - name: "group2"
             sessions:
               - "group:987654321"
   ```
2. 提高记忆重要性阈值：
   ```yaml
   plugins:
     yesimbot:
       parameters:
         memory:
           importanceThreshold: 0.6  # 提高阈值（默认为 0.5）
   ```
3. 减少上下文窗口大小：
   ```yaml
   plugins:
     yesimbot:
       parameters:
         memory:
           contextWindow: 8  # 减小窗口大小（默认为 10）
   ```

### 记忆容量问题

**症状**：日志中出现 "Memory capacity exceeded" 或相关警告，或机器人性能下降。

**解决方案**：
1. 调整记忆容量：
   ```yaml
   plugins:
     yesimbot:
       parameters:
         memory:
           maxMemoryTokens: 3000  # 减少容量（默认为 4000）
   ```
2. 启用记忆压缩：
   ```yaml
   plugins:
     yesimbot:
       parameters:
         memory:
           memoryCompression: true
           compressionThreshold: 0.75  # 压缩阈值（默认为 0.8）
   ```
3. 定期清理旧记忆：
   ```yaml
   plugins:
     yesimbot:
       maintenance:
         autoCleanMemory: true
         cleanInterval: 86400  # 每 24 小时清理一次（秒）
   ```

## 提示词配置问题

### 提示词不生效

**症状**：自定义提示词似乎没有影响机器人的行为。

**解决方案**：
1. 检查提示词配置格式：
   ```yaml
   plugins:
     yesimbot:
       prompts:
         systemPrompt: "你是一个名为 {bot_name} 的智能助手..."
         promptTemplate: |
           <system>
           {system_prompt}
           </system>
           <context>
           {context}
           </context>
           <conversation>
           {conversation_history}
           </conversation>
           <current_message>
           {current_message}
           </current_message>
   ```
2. 确保变量名称正确：
   - 检查变量名是否与系统支持的变量匹配
   - 常见变量：`{bot_name}`、`{system_prompt}`、`{context}`、`{conversation_history}`、`{current_message}`
3. 清除模型缓存：
   ```yaml
   plugins:
     yesimbot:
       parameters:
         performance:
           cacheEnabled: false  # 临时禁用缓存
   ```

### 提示词冲突

**症状**：机器人行为不一致，或表现出矛盾的特性。

**解决方案**：
1. 检查系统提示词和场景提示词是否存在冲突：
   ```yaml
   plugins:
     yesimbot:
       prompts:
         systemPrompt: "..."  # 确保与场景提示词协调
         scenarioPrompts:
           - name: "问答场景"
             prompt: "..."  # 确保与系统提示词协调
   ```
2. 简化提示词，避免过于复杂：
   - 删除不必要的指令
   - 确保指令清晰、直接
   - 避免矛盾的要求
3. 调整提示词优先级：
   ```yaml
   plugins:
     yesimbot:
       prompts:
         scenarioPrompts:
           - name: "问答场景"
             priority: 10  # 调整优先级
   ```

### 提示词过长

**症状**：日志中出现 "Prompt too long" 或 "Token limit exceeded" 错误。

**解决方案**：
1. 简化系统提示词：
   - 删除冗余内容
   - 使用更简洁的表达
2. 减少对话历史长度：
   ```yaml
   plugins:
     yesimbot:
       parameters:
         memory:
           contextWindow: 5  # 减小窗口大小（默认为 10）
   ```
3. 启用记忆压缩：
   ```yaml
   plugins:
     yesimbot:
       parameters:
         memory:
           memoryCompression: true
   ```

## 验证器配置问题

### 验证器过于严格

**症状**：大多数回复被拒绝，日志中出现大量验证失败记录。

**解决方案**：
1. 降低内容过滤器级别：
   ```yaml
   plugins:
     yesimbot:
       verifier:
         validators:
           - type: "ContentFilter"
             settings:
               filterLevel: "low"  # 降低过滤级别（默认为 "medium"）
   ```
2. 减少自定义敏感词：
   ```yaml
   plugins:
     yesimbot:
       verifier:
         validators:
           - type: "ContentFilter"
             settings:
               customWords:  # 减少或移除不必要的敏感词
                 - "敏感词1"
   ```
3. 降低一致性检查器的严格程度：
   ```yaml
   plugins:
     yesimbot:
       verifier:
         validators:
           - type: "ConsistencyChecker"
             settings:
               strictnessLevel: "low"  # 降低严格程度（默认为 "medium"）
   ```

### 验证器不生效

**症状**：验证器似乎没有过滤不适当的内容或修正格式问题。

**解决方案**：
1. 确认验证器已启用：
   ```yaml
   plugins:
     yesimbot:
       verifier:
         enabled: true  # 确保验证器已启用
   ```
2. 检查验证器优先级：
   ```yaml
   plugins:
     yesimbot:
       verifier:
         validators:
           - type: "ContentFilter"
             enabled: true
             priority: 10  # 确保优先级合理
   ```
3. 调整失败操作：
   ```yaml
   plugins:
     yesimbot:
       verifier:
         failureAction: "regenerate"  # 设置为 "regenerate"、"modify" 或 "ignore"
   ```

### 验证器性能问题

**症状**：验证过程耗时过长，导致回复延迟。

**解决方案**：
1. 减少启用的验证器数量：
   - 只保留必要的验证器
   - 禁用不必要的验证器
2. 使用并行执行模式：
   ```yaml
   plugins:
     yesimbot:
       verifier:
         validatorChain:
           executionMode: "parallel"  # 并行执行验证器
   ```
3. 设置验证器超时：
   ```yaml
   plugins:
     yesimbot:
       verifier:
         timeout: 3000  # 设置超时时间（毫秒）
   ```

## 工具系统问题

### 工具不执行

**症状**：机器人无法使用工具，或工具调用失败。

**解决方案**：
1. 确认工具系统已启用：
   ```yaml
   plugins:
     yesimbot:
       toolSystem:
         enabled: true  # 确保工具系统已启用
   ```
2. 检查特定工具是否已启用：
   ```yaml
   plugins:
     yesimbot:
       toolSystem:
         tools:
           search:
             enabled: true  # 确保特定工具已启用
   ```
3. 检查权限设置：
   ```yaml
   plugins:
     yesimbot:
       toolSystem:
         permissions:
           adminOnly: false  # 确保权限设置正确
           allowedUsers:  # 确保用户有权限
             - "123456789"
   ```

### API 密钥问题

**症状**：工具调用失败，日志中出现 API 相关错误。

**解决方案**：
1. 检查工具 API 密钥：
   ```yaml
   plugins:
     yesimbot:
       toolSystem:
         tools:
           search:
             apiKey: "your_api_key"  # 确保 API 密钥正确
   ```
2. 确认 API 密钥权限：
   - 登录 API 提供商网站
   - 检查 API 密钥权限
   - 确保 API 密钥有权访问所需功能
3. 如果使用代理，确保代理配置正确：
   ```yaml
   plugins:
     yesimbot:
       toolSystem:
         tools:
           search:
             proxy: "http://proxy-server:port"  # 设置代理
   ```

### 工具执行超时

**症状**：工具执行时间过长，导致超时错误。

**解决方案**：
1. 增加工具执行超时时间：
   ```yaml
   plugins:
     yesimbot:
       toolSystem:
         toolExecutionTimeout: 60000  # 增加超时时间（毫秒，默认为 30000）
   ```
2. 优化工具参数：
   ```yaml
   plugins:
     yesimbot:
       toolSystem:
         tools:
           search:
             maxResults: 3  # 减少结果数量
             timeout: 15000  # 设置特定工具的超时时间
   ```
3. 检查网络连接，确保工具能够访问所需的外部服务。

## 图像功能问题

### 图像生成失败

**症状**：图像生成请求失败，或生成的图像质量不佳。

**解决方案**：
1. 检查图像生成 API 配置：
   ```yaml
   plugins:
     yesimbot:
       imageViewer:
         generation:
           enabled: true
           provider: "openai"
           apiKey: "your_api_key"  # 确保 API 密钥正确
   ```
2. 调整图像生成参数：
   ```yaml
   plugins:
     yesimbot:
       imageViewer:
         generation:
           model: "dall-e-3"  # 使用更高质量的模型
           size: "1024x1024"  # 调整图像大小
           quality: "hd"  # 提高质量
   ```
3. 检查是否达到每日生成限制：
   ```yaml
   plugins:
     yesimbot:
       imageViewer:
         generation:
           maxGenerationsPerDay: 100  # 增加每日限制
   ```

### 图像查看问题

**症状**：机器人无法查看或理解用户发送的图像。

**解决方案**：
1. 确认图像查看功能已启用：
   ```yaml
   plugins:
     yesimbot:
       imageViewer:
         viewing:
           enabled: true  # 确保图像查看已启用
   ```
2. 检查支持的图像格式：
   ```yaml
   plugins:
     yesimbot:
       imageViewer:
         supportedFormats:
           - "jpg"
           - "jpeg"
           - "png"
           - "gif"
           - "webp"  # 确保包含所需格式
   ```
3. 调整图像描述详细程度：
   ```yaml
   plugins:
     yesimbot:
       imageViewer:
         viewing:
           descriptionDetail: "high"  # 提高描述详细程度
   ```

### 存储空间问题

**症状**：图像存储空间不足，或图像保存失败。

**解决方案**：
1. 调整存储设置：
   ```yaml
   plugins:
     yesimbot:
       imageViewer:
         storage:
           maxStorageSize: 2048  # 增加最大存储大小（MB）
   ```
2. 启用旧图像清理：
   ```yaml
   plugins:
     yesimbot:
       imageViewer:
         storage:
           cleanupOldImages: true
           imageRetentionDays: 15  # 减少图像保留天数
   ```
3. 更改存储路径到更大的分区：
   ```yaml
   plugins:
     yesimbot:
       imageViewer:
         storage:
           storagePath: "/path/to/larger/storage"  # 更改存储路径
   ```

## 性能和资源问题

### 内存使用过高

**症状**：系统内存使用率高，可能导致性能下降或崩溃。

**解决方案**：
1. 减少记忆容量：
   ```yaml
   plugins:
     yesimbot:
       parameters:
         memory:
           maxMemoryTokens: 3000  # 减少容量（默认为 4000）
   ```
2. 启用记忆压缩：
   ```yaml
   plugins:
     yesimbot:
       parameters:
         memory:
           memoryCompression: true
   ```
3. 减小缓存大小：
   ```yaml
   plugins:
     yesimbot:
       parameters:
         performance:
           cacheSize: 50  # 减小缓存大小（默认为 100）
   ```
4. 减少并行请求数：
   ```yaml
   plugins:
     yesimbot:
       parameters:
         performance:
           parallelRequests: 2  # 减少并行请求数（默认为 3）
   ```

### CPU 使用过高

**症状**：系统 CPU 使用率高，导致性能下降或响应延迟。

**解决方案**：
1. 减少批处理大小：
   ```yaml
   plugins:
     yesimbot:
       parameters:
         performance:
           batchSize: 3  # 减小批处理大小（默认为 5）
   ```
2. 增加请求间隔：
   ```yaml
   plugins:
     yesimbot:
       parameters:
         performance:
           requestInterval: 1000  # 增加请求间隔（毫秒）
   ```
3. 禁用不必要的功能：
   - 禁用不常用的工具
   - 禁用不必要的验证器
   - 简化提示词和记忆处理

### 响应延迟高

**症状**：机器人响应时间长，用户体验不佳。

**解决方案**：
1. 调整模型参数：
   ```yaml
   plugins:
     yesimbot:
       parameters:
         model:
           maxTokens: 800  # 减少最大令牌数（默认为 1000）
   ```
2. 使用更快的模型：
   ```yaml
   plugins:
     yesimbot:
       adapters:
         adapterList:
           - name: "faster-model"
             type: "openai"
             config:
               model: "gpt-3.5-turbo"  # 使用响应更快的模型
   ```
3. 启用缓存：
   ```yaml
   plugins:
     yesimbot:
       parameters:
         performance:
           cacheEnabled: true
           cacheSize: 200  # 增加缓存大小
   ```
4. 调整响应延迟设置：
   ```yaml
   plugins:
     yesimbot:
       parameters:
         interaction:
           responseDelay: false  # 禁用响应延迟
   ```

## 配置文件问题

### 配置文件格式错误

**症状**：启动时出现 "Invalid configuration" 或 YAML 解析错误。

**解决方案**：
1. 检查 YAML 格式：
   - 确保缩进一致（使用空格而非制表符）
   - 确保冒号后有空格
   - 确保列表项使用一致的格式
2. 使用 YAML 验证工具验证配置文件：
   ```bash
   # 安装 yamllint
   npm install -g yaml-lint
   
   # 验证配置文件
   yamllint ~/.koishi/koishi.yml
   ```
3. 恢复到默认配置，然后逐步添加自定义设置：
   ```bash
   # 备份当前配置
   cp ~/.koishi/koishi.yml ~/.koishi/koishi.yml.bak
   
   # 生成默认配置
   koishi init --default > ~/.koishi/koishi.yml
   ```

### 配置项不生效

**症状**：修改配置后，设置似乎没有生效。

**解决方案**：
1. 确保保存了配置文件并重启了 Koishi：
   ```bash
   # 重启 Koishi
   koishi stop
   koishi start
   ```
2. 检查配置路径是否正确：
   ```bash
   # 查看 Koishi 使用的配置文件
   koishi config
   ```
3. 检查配置项名称和层级是否正确：
   - 配置项名称区分大小写
   - 确保配置项位于正确的层级下
4. 尝试使用控制台界面修改配置，而不是直接编辑配置文件。

### 配置冲突

**症状**：不同配置项之间存在冲突，导致行为不一致。

**解决方案**：
1. 检查可能冲突的配置项：
   - 意愿值系统与响应模式
   - 记忆设置与提示词模板
   - 验证器与模型参数
2. 使用配置预设，避免手动配置冲突：
   ```yaml
   plugins:
     yesimbot:
       presets:
         use: "balanced_assistant"  # 使用预定义的配置预设
   ```
3. 简化配置，只保留必要的自定义设置，使用默认值处理其他设置。

## 获取帮助

如果您尝试了上述解决方案后仍然遇到配置问题，可以通过以下渠道获取帮助：

1. **查阅文档**：检查 [YesImBot 文档](https://github.com/HydroGest/YesImBot)，查看是否有更新的配置指南。
2. **提交 Issue**：在 [GitHub Issues](https://github.com/HydroGest/YesImBot/issues) 提交配置问题报告。
3. **加入社区**：加入 [QQ 交流群](https://qm.qq.com/cgi-bin/qm/qr?k=857518324) 或 [Discord 服务器](https://discord.gg/yesimbot) 寻求帮助。
4. **收集信息**：寻求帮助时，请提供以下信息：
   - YesImBot 版本
   - Koishi 版本
   - 配置文件内容（记得删除敏感信息如 API 密钥）
   - 错误信息和日志
   - 问题的详细描述和复现步骤
