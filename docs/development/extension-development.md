# 扩展开发指南

本指南将详细介绍如何为 YesImBot 开发自定义扩展插件，包括工具开发、配置管理和最佳实践。

## 快速开始

### 1. 创建扩展项目

```bash
# 创建新的 npm 项目
mkdir koishi-plugin-yesimbot-extension-myext
cd koishi-plugin-yesimbot-extension-myext
npm init -y

# 安装依赖
npm install koishi koishi-plugin-yesimbot
npm install -D typescript @types/node
```

### 2. 基础扩展结构

```typescript
// src/index.ts
import { Context, Schema } from 'koishi';
import { Extension, Tool, Success, Failed, Infer, withInnerThoughts } from 'koishi-plugin-yesimbot';

// 配置接口
interface Config {
    enabled: boolean;
    apiKey?: string;
    timeout: number;
}

// 配置 Schema
const ConfigSchema: Schema<Config> = Schema.object({
    enabled: Schema.boolean().default(true).description('是否启用扩展'),
    apiKey: Schema.string().role('secret').description('API 密钥'),
    timeout: Schema.number().default(5000).description('超时时间（毫秒）'),
});

@Extension({
    name: 'my-extension',
    display: '我的扩展',
    version: '1.0.0',
    description: '这是一个示例扩展',
    author: '你的名字',
})
export default class MyExtension {
    static readonly Config = ConfigSchema;
    
    constructor(public ctx: Context, public config: Config) {}
    
    @Tool({
        name: 'my_tool',
        description: '这是一个示例工具',
        parameters: withInnerThoughts({
            input: Schema.string().required().description('输入参数'),
            count: Schema.number().default(1).description('重复次数'),
        }),
    })
    async myTool({ input, count }: Infer<{ input: string; count: number }>) {
        if (!this.config.enabled) {
            return Failed('扩展未启用');
        }
        
        try {
            const result = input.repeat(count);
            return Success(`处理结果: ${result}`);
        } catch (error) {
            return Failed(`处理失败: ${error.message}`);
        }
    }
}
```

## 核心概念

### 1. 扩展装饰器

`@Extension` 装饰器用于标记扩展类并提供元数据：

```typescript
@Extension({
    name: 'extension-name',        // 扩展名称（必需）
    display: '显示名称',           // 用户友好的显示名称
    version: '1.0.0',             // 版本号
    description: '功能描述',       // 功能描述
    author: '作者名',              // 作者信息
    builtin?: boolean,            // 是否为内置扩展（通常为 false）
})
```

### 2. 工具装饰器

`@Tool` 装饰器用于标记工具方法：

```typescript
@Tool({
    name: 'tool_name',            // 工具名称（必需）
    description: '工具描述',       // 工具功能描述
    parameters: Schema.object({   // 参数 Schema
        param1: Schema.string().required(),
        param2: Schema.number().default(0),
    }),
    isSupported?: (session) => {  // 可选：平台支持检查
        return session.platform === 'discord';
    },
})
```

### 3. 参数处理

#### 使用 withInnerThoughts

为工具添加"内心独白"参数，让 AI 能够表达思考过程：

```typescript
parameters: withInnerThoughts({
    // 你的参数定义
    message: Schema.string().required(),
})
```

#### 参数验证

系统会自动验证参数类型和必需性：

```typescript
async myTool({ message, innerThoughts }: Infer<{ message: string }>) {
    // innerThoughts 会自动包含在参数中
    this.ctx.logger.debug(`AI 的想法: ${innerThoughts}`);
    
    // 处理逻辑
    return Success(result);
}
```

### 4. 返回值处理

工具方法必须返回 `Success` 或 `Failed` 结果：

```typescript
// 成功情况
return Success('操作成功');
return Success({ data: 'some data', status: 'ok' });

// 失败情况
return Failed('操作失败的原因');
return Failed('网络错误', { code: 500 });
```

## 高级功能

### 1. 依赖注入

扩展可以声明对其他服务的依赖：

```typescript
@Extension({...})
export default class MyExtension {
    static readonly inject = ['database', 'http'];
    
    constructor(public ctx: Context, public config: Config) {
        // 可以使用 ctx.database 和 ctx.http
    }
}
```

### 2. 提示词片段注册

扩展可以向 AI 的提示词中注入动态内容：

```typescript
constructor(public ctx: Context, public config: Config) {
    // 注册提示词片段
    ctx.yesimbot.prompt.registerSnippet('my-extension.status', async (session) => {
        return `当前扩展状态: ${this.config.enabled ? '启用' : '禁用'}`;
    });
}
```

### 3. 数据库集成

使用 Koishi 的数据库服务存储数据：

```typescript
constructor(public ctx: Context, public config: Config) {
    // 扩展数据库表
    ctx.model.extend('my_extension_data', {
        id: 'unsigned',
        userId: 'string',
        data: 'json',
        createdAt: 'timestamp',
    });
}

@Tool({...})
async saveData({ userId, data }: Infer<{ userId: string; data: any }>) {
    await this.ctx.database.create('my_extension_data', {
        userId,
        data,
        createdAt: new Date(),
    });
    
    return Success('数据保存成功');
}
```

### 4. 平台兼容性检查

为不同平台提供不同的工具实现：

```typescript
@Tool({
    name: 'platform_specific_tool',
    description: '平台特定工具',
    parameters: Schema.object({...}),
    isSupported: (session) => {
        // 只在 Discord 平台上可用
        return session.platform === 'discord';
    },
})
async platformTool(args: any) {
    // 实现逻辑
}
```

## 配置管理

### 1. 动态配置

支持运行时配置更新：

```typescript
constructor(public ctx: Context, public config: Config) {
    // 监听配置变化
    ctx.on('config', (config) => {
        this.config = config;
        this.onConfigUpdate();
    });
}

private onConfigUpdate() {
    // 处理配置更新逻辑
}
```

### 2. 配置验证

在 Schema 中添加验证逻辑：

```typescript
const ConfigSchema = Schema.object({
    apiKey: Schema.string()
        .required()
        .pattern(/^[a-zA-Z0-9]{32}$/)
        .description('32位API密钥'),
    
    maxRetries: Schema.number()
        .min(1)
        .max(10)
        .default(3)
        .description('最大重试次数'),
});
```

## 最佳实践

### 1. 错误处理

```typescript
@Tool({...})
async myTool(args: any) {
    try {
        // 验证输入
        if (!args.input) {
            return Failed('缺少必需的输入参数');
        }
        
        // 执行操作
        const result = await this.performOperation(args.input);
        
        // 验证结果
        if (!result) {
            return Failed('操作未返回有效结果');
        }
        
        return Success(result);
    } catch (error) {
        // 记录错误
        this.ctx.logger.error('工具执行失败:', error);
        
        // 返回用户友好的错误信息
        return Failed(`操作失败: ${error.message}`);
    }
}
```

### 2. 日志记录

```typescript
constructor(public ctx: Context, public config: Config) {
    this.logger = ctx.logger('my-extension');
}

@Tool({...})
async myTool(args: any) {
    this.logger.info('开始执行工具', args);
    
    try {
        const result = await this.performOperation(args);
        this.logger.info('工具执行成功', { result });
        return Success(result);
    } catch (error) {
        this.logger.error('工具执行失败', error);
        return Failed(error.message);
    }
}
```

### 3. 资源清理

```typescript
constructor(public ctx: Context, public config: Config) {
    // 注册清理函数
    ctx.on('dispose', () => {
        this.cleanup();
    });
}

private cleanup() {
    // 清理资源，如关闭连接、清除定时器等
}
```

## 发布和分发

### 1. 包结构

```
koishi-plugin-yesimbot-extension-myext/
├── src/
│   └── index.ts
├── lib/                 # 编译输出
├── package.json
├── tsconfig.json
└── README.md
```

### 2. package.json 配置

```json
{
  "name": "koishi-plugin-yesimbot-extension-myext",
  "version": "1.0.0",
  "description": "YesImBot 扩展：我的扩展",
  "main": "lib/index.js",
  "types": "lib/index.d.ts",
  "keywords": ["koishi", "plugin", "yesimbot", "extension"],
  "koishi": {
    "description": {
      "zh": "我的扩展功能描述"
    }
  },
  "peerDependencies": {
    "koishi": "^4.0.0",
    "koishi-plugin-yesimbot": "^3.0.0"
  }
}
```

### 3. 构建和发布

```bash
# 编译 TypeScript
npx tsc

# 发布到 npm
npm publish
```

## 调试技巧

### 1. 启用调试日志

```yaml
system:
  debug:
    enable: true
```

### 2. 工具测试

在开发过程中，可以通过直接调用工具方法进行测试：

```typescript
// 在扩展中添加测试方法
async testTool() {
    const result = await this.myTool({ input: 'test', count: 2 });
    console.log('测试结果:', result);
}
```

### 3. 热重载

启用工具热重载功能进行开发：

```yaml
capabilities:
  tools:
    advanced:
      hotReload: true  # 仅在开发环境使用
```
