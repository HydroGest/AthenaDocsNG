# 安装指南

本指南将帮助您安装 YesImBot 插件到您的 Koishi 环境中。确保您已完成[环境准备](prerequisites.md)中的所有步骤，然后按照以下说明进行操作。

## 通过 Koishi 插件市场安装（推荐）

使用 Koishi 插件市场是安装 YesImBot 最简单的方法，特别适合新手用户。

### 步骤 1: 打开 Koishi 控制台

启动 Koishi 应用程序，并在浏览器中打开控制台（通常是 `http://localhost:5140`）。

### 步骤 2: 访问插件市场

1. 在 Koishi 控制台左侧菜单中，点击"插件市场"
2. 在搜索框中输入 `yesimbot`
3. 在搜索结果中找到 `koishi-plugin-yesimbot`

### 步骤 3: 安装插件

1. 点击 `koishi-plugin-yesimbot` 插件卡片
2. 点击"安装"按钮
3. 等待安装完成

### 步骤 4: 安装扩展（可选）

如果您需要 MCP 扩展功能，可以同样的方式搜索并安装 `koishi-plugin-yesimbot-extension-mcp`。

## 通过 npm 命令行安装

如果您使用命令行方式管理 Koishi，可以通过 npm 安装 YesImBot。

### 步骤 1: 进入 Koishi 项目目录

```bash
cd 您的Koishi项目目录
```

### 步骤 2: 安装 YesImBot 核心插件

```bash
npm install koishi-plugin-yesimbot
```

### 步骤 3: 安装 MCP 扩展（可选）

```bash
npm install koishi-plugin-yesimbot-extension-mcp
```

### 步骤 4: 更新 Koishi 配置

编辑您的 `koishi.yml` 或 `koishi.config.js` 文件，添加 YesImBot 插件：

对于 YAML 配置：

```yaml
plugins:
  yesimbot:
    # 这里将添加 YesImBot 的配置
  yesimbot-extension-mcp:
    # 这里将添加 MCP 扩展的配置（如果已安装）
```

对于 JavaScript 配置：

```javascript
module.exports = {
  plugins: {
    yesimbot: {
      // 这里将添加 YesImBot 的配置
    },
    'yesimbot-extension-mcp': {
      // 这里将添加 MCP 扩展的配置（如果已安装）
    }
  }
}
```

## 从源代码安装（开发者选项）

如果您是开发者或需要最新的功能，可以从源代码安装 YesImBot。

### 步骤 1: 克隆仓库

```bash
git clone https://github.com/HydroGest/YesImBot.git
cd YesImBot
```

### 步骤 2: 安装依赖

```bash
npm install
```

### 步骤 3: 构建项目

```bash
npm run build
```

### 步骤 4: 链接到您的 Koishi 项目

```bash
# 在 YesImBot 目录中
npm link

# 在您的 Koishi 项目目录中
npm link koishi-plugin-yesimbot
```

## 验证安装

安装完成后，您可以通过以下步骤验证 YesImBot 是否正确安装：

1. 重启 Koishi 服务
2. 在 Koishi 控制台中，导航到"插件配置"
3. 检查是否存在 YesImBot 的配置选项
4. 如果安装了 MCP 扩展，也应该能看到相应的配置选项

## 常见安装问题

### 找不到插件

如果在插件市场中搜索不到 YesImBot，可能是因为：

- 您的 Koishi 版本过低，需要更新到最新版本
- 插件市场连接不稳定，可以尝试刷新页面或稍后再试
- 您可能需要添加自定义插件源

解决方案：

1. 更新 Koishi 到最新版本
2. 检查网络连接
3. 如果问题仍然存在，尝试通过 npm 命令行安装

### 安装失败

如果安装过程中出现错误，可能是因为：

- 网络连接问题
- 依赖冲突
- 权限问题

解决方案：

1. 检查网络连接
2. 尝试清除 npm 缓存：`npm cache clean --force`
3. 确保您有足够的权限安装插件
4. 查看错误日志，了解具体问题

### 版本兼容性问题

如果安装后插件无法正常工作，可能是版本兼容性问题：

- YesImBot 与当前 Koishi 版本不兼容
- YesImBot 核心与 MCP 扩展版本不匹配

解决方案：

1. 确保您使用的是最新版本的 Koishi
2. 确保 YesImBot 核心与 MCP 扩展版本匹配
3. 查看 YesImBot 的 GitHub 仓库，了解版本兼容性信息

## 下一步

成功安装 YesImBot 后，您需要进行[基本配置](basic-configuration.md)才能开始使用。
