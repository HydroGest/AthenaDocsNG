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

## 开发自己的扩展

扩展是为 YesImBot 添加新功能的最佳方式。

### 创建新工具

开发新工具是目前最主要的扩展方式。

1.  **创建 Koishi 插件:** 首先，您需要创建一个标准的 Koishi 插件。
2.  **注入 ToolService:** 在您的插件中，注入 YesImBot 的 `ToolService`。
    ```typescript
    import { Tool, ToolService } from 'koishi-plugin-yesimbot'
    import { Context, Service } from 'koishi'

    class MyExtension extends Service {
      // 注入 ToolService
      @Service.Inject()
      private tools: ToolService

      constructor(ctx: Context) {
        super(ctx, 'my-extension', true)
      }

      // 在 start 生命周期中注册工具
      start() {
        const myCoolTool: Tool = {
          name: 'my_cool_tool',
          description: '这是一个很酷的工具，它能做某件事。',
          parameters: {
            type: 'object',
            properties: {
              some_arg: { type: 'string', description: '某个参数' }
            },
            required: ['some_arg']
          },
          async execute(args) {
            // 工具的执行逻辑
            const result = `你提供的参数是：${args.some_arg}`;
            return {
              type: 'text',
              content: result
            };
          }
        }
        this.tools.register(myCoolTool)
      }
    }
    ```
3.  **注册服务:** 不要忘记在您的插件入口文件中注册这个服务 `ctx.plugin(MyExtension)`。

只要用户同时安装了 `koishi-plugin-yesimbot` 和您的扩展插件，`ToolService` 就会自动发现并注册您定义的工具，使其可供 AI 使用。