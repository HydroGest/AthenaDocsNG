# 安装指南

## 前置要求

在安装 YesImBot 之前，请确保您的环境满足以下要求：

-   ✅ 您需要一个正在运行的 Koishi 实例（版本 4.18.7 或更高）。
-   ✅ Node.js 版本 18 或更高。
-   ✅ 熟悉 Koishi 的基本操作，能够进入控制台或编辑配置文件。
-   ✅ 已配置 Koishi 数据库服务（MySQL、PostgreSQL、SQLite 等），这是记忆和历史功能所必需的。

!!! info "Koishi 新手？"
    如果您还没有安装 Koishi，请先访问 [Koishi 官方文档](https://koishi.chat/) 了解如何安装和配置。

## 第一步：安装核心插件

`koishi-plugin-yesimbot` 是运行一切功能的基础。

### 方式一：通过 Koishi 插件市场 (推荐)

1.  在 Koishi 控制台的左侧菜单中，点击 "插件市场"。
2.  在搜索框中输入 `yesimbot`。
3.  找到 `koishi-plugin-yesimbot` 并点击“安装”。

### 方式二：通过命令行

在您的 Koishi 项目目录下，执行以下命令：

```bash
npm install koishi-plugin-yesimbot
```

## 第二步：安装扩展插件 (按需)

YesImBot 的许多高级功能由扩展插件提供。请根据您的需求选择安装。

### 代码解释器 (Code Interpreter)

-   **功能:** 允许 AI 在一个安全的沙箱环境中执行 JavaScript 代码，进行计算、数据处理等操作。
-   **安装:**
    -   插件市场搜索：`yesimbot-extension-code-interpreter`
    -   命令行：`npm install koishi-plugin-yesimbot-extension-code-interpreter`

### 好感度系统 (Favor System)

-   **功能:** 为机器人添加好感度管理能力，AI 可以根据交互调整与用户的好感度。
-   **安装:**
    -   插件市场搜索：`yesimbot-extension-favor`
    -   命令行：`npm install koishi-plugin-yesimbot-extension-favor`

### 模型上下文协议 (MCP)

-   **功能:** 允许连接外部工具服务器，动态扩展 AI 的工具集。
-   **安装:**
    -   插件市场搜索：`yesimbot-extension-mcp`
    -   命令行：`npm install koishi-plugin-yesimbot-extension-mcp`

!!! tip "扩展插件安装建议"
    -   **新手用户:** 推荐安装**代码解释器**，它能显著增强 AI 的实用能力。
    -   **社群运营:** **好感度系统**能让机器人更好地融入社群。
    -   **高级开发者:** **MCP** 扩展为系统集成提供了无限可能性。

## 第三步：启用插件

1.  安装完成后，前往 Koishi 控制台的“插件配置”页面。
2.  在插件列表中找到 `yesimbot` 及您安装的扩展。
3.  点击开关以启用插件。

启用后，请查看 Koishi 控制台的日志，确认没有与插件相关的错误信息。

## 下一步

恭喜您，安装已经完成！现在，请移步到下一章节，完成机器人的首次配置。

➡️ **[快速上手](quick-start.md)**