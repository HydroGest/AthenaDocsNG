# 开发者指南

本章节面向希望扩展、定制或深入了解 YesImBot 内部工作机制的开发者。我们将介绍 YesImBot 的架构设计、API 接口、插件开发方法以及如何贡献代码。

## 架构概述

YesImBot 基于 Koishi 框架开发，采用模块化设计，主要由以下核心组件构成：

### 核心组件

1. **意愿值系统（WillingnessSystem）**：
   - 负责计算机器人回应消息的意愿度
   - 管理回应阈值和触发条件
   - 实现自然的对话参与行为

2. **记忆系统（MemorySystem）**：
   - 管理对话历史和上下文信息
   - 实现长短期记忆机制
   - 处理记忆压缩和重要性评估

3. **适配器系统（AdapterSystem）**：
   - 连接不同的语言模型 API
   - 实现负载均衡和故障转移
   - 处理请求格式转换和响应解析

4. **验证器系统（VerifierSystem）**：
   - 验证和过滤模型响应
   - 实现内容安全检查
   - 确保回应质量和一致性

5. **工具系统（ToolSystem）**：
   - 管理和执行各种工具功能
   - 处理工具调用和结果集成
   - 实现权限控制和使用限制

6. **人格系统（PersonalitySystem）**：
   - 定义和管理机器人的人格特征
   - 影响回应风格和行为模式
   - 实现情绪模拟和表达

### 架构图

```
+---------------------+
|    Koishi 框架      |
+---------------------+
          |
+---------v-----------+
|   YesImBot 核心     |
+---------+-----------+
          |
+---+-----+-----+-----+---+
|   |     |     |     |   |
v   v     v     v     v   v
+---+--+ +---+ +---+ +---+ +---+
|意愿值| |记忆| |适配| |验证| |工具|
|系统  | |系统| |器系| |器系| |系统|
+------+ +---+ |统 | |统 | +---+
              +---+ +---+
                |     |
        +-------+-----+-------+
        |      人格系统       |
        +-------------------+
```

### 数据流

1. **输入处理流程**：
   - 接收用户消息
   - 计算意愿值
   - 决定是否回应
   - 准备上下文和记忆
   - 构建提示词

2. **模型调用流程**：
   - 选择适配器
   - 发送请求到语言模型
   - 接收模型响应
   - 验证和过滤响应
   - 返回最终回应

3. **记忆管理流程**：
   - 评估对话重要性
   - 存储重要信息
   - 压缩和整理记忆
   - 选择相关记忆用于上下文

## 开发环境设置

### 前置要求

- Node.js 16.x 或更高版本
- npm 7.x 或更高版本
- Git
- 基本的 TypeScript 知识
- Koishi 框架基础

### 克隆代码库

```bash
# 克隆主仓库
git clone https://github.com/HydroGest/YesImBot.git

# 进入项目目录
cd YesImBot

# 安装依赖
npm install
```

### 开发环境配置

1. 创建开发配置文件：

```bash
# 复制示例配置
cp config.example.yml config.dev.yml

# 编辑开发配置
nano config.dev.yml
```

2. 配置开发环境变量：

```bash
# 创建环境变量文件
cp .env.example .env

# 编辑环境变量
nano .env
```

3. 启动开发服务器：

```bash
# 启动开发模式
npm run dev
```

## API 参考

YesImBot 提供了多个内部 API，可用于扩展功能或与其他系统集成。

### 意愿值系统 API

```typescript
// 计算意愿值
calculateWillingness(message: Message): number

// 检查是否应该回应
shouldRespond(message: Message): boolean

// 更新意愿值配置
updateWillingnessConfig(config: WillingnessConfig): void
```

### 记忆系统 API

```typescript
// 添加记忆
addMemory(session: string, content: string, importance?: number): Promise<void>

// 获取相关记忆
getRelevantMemories(session: string, query: string, limit?: number): Promise<Memory[]>

// 清理旧记忆
cleanupOldMemories(session: string, olderThan?: number): Promise<number>
```

### 适配器系统 API

```typescript
// 发送请求到模型
sendModelRequest(prompt: string, options?: ModelRequestOptions): Promise<ModelResponse>

// 获取可用适配器列表
getAvailableAdapters(): Adapter[]

// 切换默认适配器
setDefaultAdapter(adapterName: string): boolean
```

### 验证器系统 API

```typescript
// 验证响应内容
validateResponse(response: string, context: ValidationContext): Promise<ValidationResult>

// 添加自定义验证器
addValidator(validator: Validator): void

// 获取验证统计信息
getValidationStats(): ValidationStats
```

### 工具系统 API

```typescript
// 注册新工具
registerTool(tool: Tool): void

// 执行工具
executeTool(toolName: string, params: any): Promise<ToolResult>

// 检查工具权限
checkToolPermission(userId: string, toolName: string): boolean
```

## 插件开发

YesImBot 支持通过插件机制扩展功能。以下是开发插件的基本步骤：

### 插件结构

一个典型的 YesImBot 插件结构如下：

```
my-plugin/
├── src/
│   ├── index.ts       # 插件入口
│   ├── commands.ts    # 命令定义
│   └── services.ts    # 服务实现
├── package.json       # 包信息
├── tsconfig.json      # TypeScript 配置
└── README.md          # 文档
```

### 插件模板

可以使用以下命令创建插件模板：

```bash
# 创建插件模板
npm init koishi-plugin my-plugin

# 进入插件目录
cd koishi-plugin-my-plugin

# 安装依赖
npm install
```

### 基本插件示例

```typescript
// src/index.ts
import { Context, Schema } from 'koishi'

// 定义插件配置结构
export interface Config {
  message: string
}

// 定义配置模式
export const Config: Schema<Config> = Schema.object({
  message: Schema.string().default('Hello, world!').description('欢迎消息'),
})

// 插件主函数
export function apply(ctx: Context, config: Config) {
  // 注册命令
  ctx.command('hello')
    .action(() => config.message)
  
  // 访问 YesImBot API
  const yesimbot = ctx.yesimbot
  if (yesimbot) {
    // 使用 YesImBot API
    ctx.middleware((session, next) => {
      // 在这里处理消息
      return next()
    })
  }
}
```

### 发布插件

1. 准备发布：

```bash
# 构建插件
npm run build

# 测试插件
npm test
```

2. 发布到 npm：

```bash
# 登录 npm
npm login

# 发布插件
npm publish
```

## 自定义组件开发

### 自定义验证器

```typescript
// 创建自定义验证器
import { Validator, ValidationContext, ValidationResult } from 'yesimbot'

export class CustomValidator implements Validator {
  name = 'CustomValidator'
  priority = 10
  
  async validate(response: string, context: ValidationContext): Promise<ValidationResult> {
    // 实现验证逻辑
    const isValid = /* 验证逻辑 */
    
    return {
      valid: isValid,
      reason: isValid ? '' : '验证失败的原因',
      modified: isValid ? response : '修改后的响应'
    }
  }
}

// 注册验证器
export function apply(ctx: Context) {
  const verifier = ctx.yesimbot.verifier
  verifier.addValidator(new CustomValidator())
}
```

### 自定义工具

```typescript
// 创建自定义工具
import { Tool, ToolParams, ToolResult } from 'yesimbot'

export class CustomTool implements Tool {
  name = 'CustomTool'
  description = '这是一个自定义工具'
  parameters = {
    query: {
      type: 'string',
      description: '查询参数'
    }
  }
  
  async execute(params: ToolParams): Promise<ToolResult> {
    const { query } = params
    
    // 实现工具逻辑
    const result = /* 工具执行逻辑 */
    
    return {
      success: true,
      data: result,
      message: '工具执行成功'
    }
  }
}

// 注册工具
export function apply(ctx: Context) {
  const toolSystem = ctx.yesimbot.toolSystem
  toolSystem.registerTool(new CustomTool())
}
```

### 自定义适配器

```typescript
// 创建自定义适配器
import { Adapter, ModelRequestOptions, ModelResponse } from 'yesimbot'

export class CustomAdapter implements Adapter {
  name = 'CustomAdapter'
  type = 'custom'
  priority = 5
  weight = 1
  
  async initialize(config: any): Promise<boolean> {
    // 初始化适配器
    return true
  }
  
  async sendRequest(prompt: string, options: ModelRequestOptions): Promise<ModelResponse> {
    // 实现请求逻辑
    const response = /* 发送请求到自定义 API */
    
    return {
      content: response,
      usage: {
        promptTokens: 0,
        completionTokens: 0,
        totalTokens: 0
      },
      raw: response
    }
  }
}

// 注册适配器
export function apply(ctx: Context) {
  const adapterSystem = ctx.yesimbot.adapterSystem
  adapterSystem.registerAdapter(new CustomAdapter())
}
```

## 贡献指南

我们欢迎社区贡献，以下是参与 YesImBot 开发的指南：

### 提交 Issue

1. 使用 Issue 模板提交问题报告或功能请求
2. 提供详细的复现步骤和环境信息
3. 如果可能，附上相关日志和截图

### 提交 Pull Request

1. Fork 仓库并创建功能分支
2. 遵循项目的代码风格和命名约定
3. 编写单元测试覆盖新功能
4. 确保所有测试通过
5. 更新文档以反映更改
6. 提交 Pull Request 并填写 PR 模板

### 代码风格

YesImBot 使用 ESLint 和 Prettier 维护代码风格：

```bash
# 检查代码风格
npm run lint

# 自动修复代码风格问题
npm run lint:fix
```

### 测试

提交代码前，请确保通过所有测试：

```bash
# 运行单元测试
npm test

# 运行特定测试
npm test -- -t "测试名称"
```

## 调试技巧

### 日志调试

YesImBot 使用分级日志系统，可以通过配置启用详细日志：

```yaml
plugins:
  yesimbot:
    debug:
      logLevel: "verbose"  # 可选值: error, warn, info, verbose, debug
      logToFile: true
      logFilePath: "./logs/yesimbot.log"
```

### 调试模式

启用调试模式可以获取更多内部信息：

```yaml
plugins:
  yesimbot:
    debug:
      debugMode: true
      logInternalState: true
      logPrompts: true
      logResponses: true
```

### 性能分析

使用内置的性能分析工具：

```yaml
plugins:
  yesimbot:
    debug:
      performanceMonitoring: true
      slowOperationThreshold: 1000  # 毫秒
```

## 常见开发问题

### TypeScript 类型错误

**问题**：编译时遇到类型错误。

**解决方案**：
1. 确保使用正确的类型定义
2. 检查 `tsconfig.json` 配置
3. 更新依赖版本

### API 变更

**问题**：升级后 API 不兼容。

**解决方案**：
1. 查阅 CHANGELOG 了解 API 变更
2. 使用适配层处理不同版本的 API
3. 考虑使用稳定版本的 API

### 内存泄漏

**问题**：长时间运行后内存使用增加。

**解决方案**：
1. 使用 Node.js 内存分析工具
2. 检查循环引用和未释放的资源
3. 实现定期清理机制

## 高级主题

### 性能优化

1. **请求批处理**：
   - 合并多个小请求为一个批处理请求
   - 实现请求队列和优先级处理

2. **缓存策略**：
   - 实现多级缓存系统
   - 使用 LRU 缓存算法
   - 设置合理的缓存过期策略

3. **并行处理**：
   - 使用 Worker 线程处理计算密集型任务
   - 实现异步处理管道

### 安全最佳实践

1. **输入验证**：
   - 验证所有用户输入
   - 防止注入攻击

2. **权限控制**：
   - 实现细粒度的权限系统
   - 使用最小权限原则

3. **敏感数据处理**：
   - 加密存储敏感信息
   - 实现数据脱敏机制

### 扩展架构

1. **微服务集成**：
   - 将 YesImBot 组件拆分为独立服务
   - 使用消息队列进行通信
   - 实现服务发现和负载均衡

2. **分布式部署**：
   - 设计分布式记忆存储
   - 实现状态同步机制
   - 处理分布式环境中的一致性问题

## 资源和参考

### 官方资源

- [YesImBot GitHub 仓库](https://github.com/HydroGest/YesImBot)
- [API 文档](https://github.com/HydroGest/YesImBot/wiki/API)
- [开发者论坛](https://github.com/HydroGest/YesImBot/discussions)

### 社区资源

- [插件目录](https://github.com/HydroGest/YesImBot-plugins)
- [示例项目](https://github.com/HydroGest/YesImBot-examples)
- [常见问题解答](https://github.com/HydroGest/YesImBot/wiki/FAQ)

### 学习资源

- [Koishi 开发文档](https://koishi.chat/zh-CN/guide/)
- [TypeScript 官方文档](https://www.typescriptlang.org/docs/)
- [Node.js 最佳实践](https://github.com/goldbergyoni/nodebestpractices)
