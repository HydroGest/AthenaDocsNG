# 开发者指南

本指南为希望为 YesImBot 贡献代码，或基于其进行二次开发的开发者提供信息。

## 项目架构

-   **Monorepo 结构:** YesImBot 采用 monorepo 结构，源代码位于 `packages/*` 目录下。
    -   `packages/core`: 核心插件 `koishi-plugin-yesimbot`。
    -   `packages/extensions/*`: 各个官方扩展插件。
-   **服务导向架构 (SOA):** 核心功能被拆分为多个独立的服务（如 `ModelService`, `ToolService`, `MemoryService` 等），通过依赖注入（DI）进行管理和交互。这种设计使得代码解耦，易于维护和测试。更多信息请参考 [智能体架构](../concepts/agent-architecture.md)。

## 如何贡献

我们欢迎任何形式的贡献！

1.  **报告 Bug 或提出建议:** 请通过 GitHub Issues 提交您发现的问题或新功能建议。
2.  **贡献代码:**
    -   Fork 本项目到您的 GitHub 账户。
    -   基于 `main` 分支创建一个新的特性分支，例如 `feature/my-new-feature`。
    -   在您的分支上进行修改和开发。
    -   确保您的代码遵循项目的编码风格，并通过 lint 检查。
    -   提交 Pull Request 到主项目的 `main` 分支，并详细描述您的修改内容。

# 开发一个新的工具扩展

扩展是为 YesImBot 添加新功能的最佳方式。通过创建扩展，你可以封装一组相关的 "工具" (Tools)，让 AI 智能体能够调用你编写的函数来与外部 API 或服务进行交互。

本文档将指导你完成从零到一的完整开发流程。

## 核心概念

在开始之前，请确保你理解以下几个核心概念：

*   **扩展 (Extension)**: 一个工具的集合，以一个带有 `@Extension` 装饰器的类的形式存在。它是一个标准的 Koishi 插件。
*   **工具 (Tool)**: 一个具体的功能，以一个带有 `@Tool` 装饰器的类方法的形式存在。AI 将根据其元数据（特别是 `description`）来决定何时调用它。
*   **元数据 (Metadata)**: 描述扩展和工具信息的数据。`ExtensionMetadata` 用于描述扩展包，`ToolMetadata` 用于描述单个工具。
*   **Schema**: 我们使用 Koishi 的 `Schema` 来定义工具的参数。它不仅能提供强大的类型验证，还能自动生成配置界面。

## 步骤一：创建扩展类并添加元数据

首先，创建一个 TypeScript 文件（例如 `my-extension.ts`），并定义一个类。然后，使用 `@Extension` 装饰器来标记这个类，并提供必要的元数据。

```typescript
import { Context, Schema } from 'koishi';
import { Extension, IExtension } from 'koishi-plugin-yesimbot/services';

@Extension({
    name: 'my-awesome-extension', // 扩展的唯一标识，建议使用 npm 包名格式
    display: '我的超棒扩展',      // 在UI中显示的名称
    description: '一个演示如何创建扩展的示例项目。',
    author: 'Your Name',
    version: '1.0.0',
})
export default class MyAwesomeExtension implements IExtension {
    // Koishi的Context和扩展的配置会自动注入
    // IExtension 接口所需的属性将由装饰器自动实现
    constructor(public ctx: Context, public config: any) {}

    // ... 工具将在这里定义 ...
}
```

**关键点:**

*   `@Extension` 装饰器是必需的，它负责将您的类转换为一个可被 `ToolService` 识别和加载的扩展。
*   `name` 字段必须是唯一的，它将作为扩展的标识符。
*   实现 `IExtension` 接口是可选的，但推荐这样做以获得更好的类型提示。`metadata` 和 `tools` 属性会被装饰器自动处理。

## 步骤二：定义扩展的配置 (可选)

如果您的扩展需要用户进行配置，您可以在类中定义一个静态的 `Config` 属性，它应该是一个 `Schema` 对象。`ToolService` 会自动处理配置的加载、验证，并将其显示在 Koishi 控制台中。

```typescript
// ... imports ...

// 为配置定义一个接口，以获得更好的类型支持
interface MyAwesomeExtensionConfig {
    greeting: string;
    enableAdvancedFeatures: boolean;
}

@Extension({ /* ... metadata ... */ })
export default class MyAwesomeExtension implements IExtension {
    // 定义扩展的配置 Schema
    static readonly Config: Schema<MyAwesomeExtensionConfig> = Schema.object({
        greeting: Schema.string().default('Hello').description('要使用的问候语。'),
        enableAdvancedFeatures: Schema.boolean().default(false).description('是否启用高级功能。'),
    });

    // 构造函数中可以访问到经过验证和填充默认值后的配置
    constructor(public ctx: Context, public config: MyAwesomeExtensionConfig) {
        this.ctx.logger.info(`MyAwesomeExtension 已加载，问候语为: ${this.config.greeting}`);
    }

    // ...
}
```

**关键点:**

*   `Config` 必须是 `static readonly` 的。
*   你可以在构造函数和所有工具方法中通过 `this.config` 安全地访问到类型正确的配置项。

## 步骤三：使用 `@Tool` 装饰器创建工具

在扩展类中，将您希望暴露给 AI 的方法使用 `@Tool` 装饰器进行标记。您需要为每个工具提供详细的描述和参数定义。

```typescript
import { Schema } from 'koishi';
import { Tool, withInnerThoughts, Success, Failed, Infer } from 'koishi-plugin-yesimbot';

// ... 在 MyAwesomeExtension 类内部 ...

@Tool({
    name: 'say_hello', // 可选，若不提供则使用方法名 'sayHello'
    description: '向指定的人发送一句问候。可以自定义问候语。',
    parameters: withInnerThoughts({ // 推荐使用 withInnerThoughts 包装
        name: Schema.string().required().description('要问候的人的姓名。'),
        language: Schema.union(['en', 'zh-CN']).default('en').description('使用的语言。')
    }),
})
async sayHello(args: Infer<{ name: string; language: 'en' | 'zh-CN' }>) {
    // args 对象包含了 session 和所有经过验证的参数
    const { session, name, language } = args;

    if (!session) {
        return Failed('此工具只能在会话上下文中使用。');
    }

    const greeting = language === 'zh-CN' ? '你好' : this.config.greeting;
    const message = `${greeting}, ${name}!`;
    await session.send(message);

    // 返回一个 ToolCallResult 对象
    return Success({ messageSent: message });
}
```

**关键点:**

*   `description` 字段至关重要，LLM 将根据它来决定何时以及如何使用此工具。请务必写得**清晰、准确、详细**。
*   `parameters` 字段是一个 `Schema` 对象，用于定义工具的输入参数。
    *   使用 `.required()` 标记必要参数。
    *   务必为每个参数添加 `.description()`。
    *   推荐使用 `withInnerThoughts()` 辅助函数来包装您的参数，这允许 LLM 在调用工具时提供其“内心独白”，有助于调试和理解其行为。
*   工具方法必须是 `async` 的，并且应该返回 `Promise<ToolCallResult>`。
*   使用 `Success(result)` 和 `Failed(error)` 辅助函数来创建返回结果。`Failed` 结果可以包含一个 `retryable: true` 字段来建议 `ToolService` 进行重试。
*   工具方法的参数是一个对象，它会自动接收到 `session` (如果可用)以及所有在 `parameters` 中定义的参数。使用 `Infer<T>` 类型可以获得完整的类型提示。

## 步骤四：控制工具的可用性 (可选)

有时，一个工具可能只在特定的平台或满足某些条件的会话中可用。您可以通过在 `ToolMetadata` 中提供 `isSupported` 函数来实现这一点。

```typescript
// ... 在 MyAwesomeExtension 类内部 ...

@Tool({
    name: 'platform_specific_feature',
    description: '一个只在特定平台上可用的功能。',
    parameters: Schema.object({}),
    // isSupported 是一个接收 session 对象并返回布尔值的函数
    isSupported: (session) => session.platform === 'onebot', // 示例：只在 onebot 平台可用
})
async platformSpecificFeature(args: Infer<{}>) {
    // ... 实现 ...
    return Success();
}
```

**关键点:**

*   如果 `isSupported` 函数返回 `false`，`ToolService` 将不会在当前会话中提供此工具，AI 将无法看到也无法调用它。
*   如果不提供 `isSupported` 函数，工具默认在所有会话中都可用。

## API 参考

### 装饰器

*   `@Extension(metadata: ExtensionMetadata): ClassDecorator`
    *   **作用**: 将一个类转换为工具扩展插件。
*   `@Tool(metadata: ToolMetadata<TParams>): MethodDecorator`
    *   **作用**: 将一个类方法声明为工具。

### 核心类型

*   `IExtension<TConfig>`: 扩展类应实现的接口（可选，用于类型提示）。
*   `ToolCallResult<TResult>`: 工具执行后返回的结果对象。
*   `ExtensionMetadata`: 扩展的元数据定义。
*   `ToolMetadata<TParams>`: 工具的元数据定义。
*   `Infer<T>`: 从 Schema 推断出工具参数的类型，并自动合并 `session`。

### 辅助函数

*   `Success<T>(result?: T, metadata?: ...): ToolCallResult<T>`: 创建一个表示成功的 `ToolCallResult`。
*   `Failed(error: string, metadata?: ...): ToolCallResult`: 创建一个表示失败的 `ToolCallResult`。
*   `withInnerThoughts(params: { [T: string]: Schema<any> }): Schema<any>`: 为工具参数添加 `inner_thoughts` 字段。