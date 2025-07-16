# 扩展插件：MCP

**插件名:** `koishi-plugin-yesimbot-extension-mcp`

MCP (Model Context Protocol) 扩展是 YesImBot 最具潜力的工具扩展方式。它允许 YesImBot 连接到一个或多个外部的“工具服务器”，动态地将这些服务器提供的工具注册到自己的工具系统中。

## 功能

-   **动态工具注册:** 无需重启 Koishi 或修改插件代码，只要连接上 MCP 服务器，就能即时获得新工具。
-   **跨语言工具:** MCP 服务器可以使用任何语言编写（Python, Go, Rust...），只要它遵循 MCP 协议，就能为 YesImBot 提供工具。
-   **本地命令执行:** 除了连接远程服务器，该扩展也可以将本地命令行程序包装成 AI 可用的工具。
-   **内置环境管理:** 可自动下载和管理 `uv` (用于 Python) 和 `bun` (用于 JS)，简化本地工具的运行环境配置。

## 使用场景

-   **连接专业工具集:** 运行一个专门用于科学计算的 Python MCP 服务器，为 AI 提供强大的数学能力。
-   **企业内网集成:** 部署一个 MCP 服务器在公司内网，提供查询内部数据库、调用内部 API 的工具。
-   **快速原型开发:** 使用 Python 或其他语言快速开发一个新工具，通过 MCP 对接给 AI 测试，无需编写 Koishi 插件。

## 配置

在 `capabilities.tools.extra` 下找到 `mcp` 键进行配置。

```yaml
capabilities:
  tools:
    extra:
      mcp:
        timeout: 5000
        activeTools: [] # 在此选择从服务器获取后，需要激活的工具名称
        mcpServers:
          # 示例1: 连接一个远程服务器
          my_remote_server:
            url: 'http://my-mcp-server.com/sse'
          # 示例2: 将本地 Python 脚本作为工具
          my_local_script:
            command: 'python'
            args:
              - '-u'
              - 'path/to/your/script.py'
        uvSettings:
          autoDownload: true
        bunSettings:
          autoDownload: true
```

| 键 | 类型 | 默认值 | 描述 |
|---|---|---|---|
| `timeout` | number | `5000` | 请求 MCP 服务器的超时时间(毫秒)。 |
| `activeTools` | `string[]`| `[]` | 从服务器获取的工具中，选择要启用的工具列表。 |
| `mcpServers` | `Record<string, object>`| `{}` | MCP 服务器配置列表，键为自定义名称。 |
| `uvSettings.autoDownload`| boolean | `true` | 是否自动下载和安装 UV (Python 环境)。 |
| `bunSettings.autoDownload`| boolean | `true` | 是否自动下载和安装 Bun (JS 环境)。 |
| `globalSettings.githubMirror`| string | - | 全局 GitHub 镜像地址，用于加速下载。 |