# 环境准备

在安装 YesImBot 之前，您需要确保您的系统满足以下要求，并准备好必要的环境。

## 系统要求

YesImBot 作为 Koishi 的插件运行，因此您需要先安装 Koishi 及其依赖。以下是基本的系统要求：

### 操作系统

YesImBot 支持以下操作系统：

- **Windows**: Windows 10 或更高版本
- **macOS**: macOS 10.15 (Catalina) 或更高版本
- **Linux**: 任何支持 Node.js 的现代 Linux 发行版，如 Ubuntu 20.04+、Debian 11+、CentOS 8+ 等

### 硬件要求

最低硬件配置：

- **CPU**: 双核处理器
- **内存**: 2GB RAM（推荐 4GB 或更高）
- **存储**: 1GB 可用空间（不包括数据存储）

推荐硬件配置（用于更流畅的体验）：

- **CPU**: 四核处理器或更高
- **内存**: 8GB RAM 或更高
- **存储**: 5GB 或更多可用空间

## 软件依赖

### 必需软件

1. **Node.js**: 版本 16.x 或更高（推荐使用 LTS 版本）
2. **npm**: 通常随 Node.js 一起安装
3. **Koishi**: 版本 4.x 或更高

### 可选软件

- **Git**: 用于从源代码安装或贡献代码
- **数据库**: 如 SQLite（默认）、MySQL 或 MongoDB，用于存储配置和数据
- **PM2**: 用于在生产环境中管理 Node.js 应用程序

## 安装 Node.js 和 npm

### Windows

1. 访问 [Node.js 官网](https://nodejs.org/)
2. 下载并安装 LTS 版本
3. 安装过程中，确保选择包含 npm 的选项
4. 安装完成后，打开命令提示符或 PowerShell，运行以下命令验证安装：

```bash
node -v
npm -v
```

### macOS

使用 Homebrew 安装（推荐）：

```bash
# 安装 Homebrew（如果尚未安装）
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# 安装 Node.js 和 npm
brew install node
```

或者从 [Node.js 官网](https://nodejs.org/) 下载安装包。

安装完成后，打开终端，运行以下命令验证安装：

```bash
node -v
npm -v
```

### Linux (Ubuntu/Debian)

```bash
# 更新包列表
sudo apt update

# 安装 Node.js 和 npm
sudo apt install nodejs npm

# 如果需要更新版本，可以使用 n 模块
sudo npm install -g n
sudo n lts
```

安装完成后，运行以下命令验证安装：

```bash
node -v
npm -v
```

## 安装 Koishi

YesImBot 是基于 Koishi 的插件，因此您需要先安装 Koishi。有两种方式可以安装 Koishi：

### 方式一：使用 Koishi 桌面版（推荐新手）

1. 访问 [Koishi 官网](https://koishi.chat/manual/starter/desktop.html)
2. 下载适合您操作系统的桌面版安装包
3. 安装并启动 Koishi
4. 按照界面提示完成初始设置

### 方式二：使用命令行安装

```bash
# 全局安装 Koishi CLI
npm install -g @koishijs/cli

# 创建一个新的 Koishi 应用
mkdir my-koishi-app
cd my-koishi-app
koishi init

# 按照提示完成初始化
# 启动 Koishi
koishi start
```

## 准备 LLM API 密钥

YesImBot 需要使用大型语言模型 API 来生成回复。您需要准备以下至少一种 API 的访问密钥：

### OpenAI API

1. 访问 [OpenAI 平台](https://platform.openai.com/)
2. 注册或登录您的账户
3. 导航到 API 密钥页面
4. 创建新的 API 密钥
5. 复制并安全保存您的 API 密钥

### Cloudflare AI API

1. 登录您的 [Cloudflare 账户](https://dash.cloudflare.com/)
2. 导航到 "Workers & Pages" > "AI"
3. 获取您的 API 密钥和账户 ID
4. 复制并安全保存这些信息

### Ollama（本地部署选项）

如果您希望在本地运行模型：

1. 访问 [Ollama 官网](https://ollama.ai/)
2. 下载并安装适合您系统的版本
3. 按照说明拉取所需的模型（如 llama2）

```bash
ollama pull llama2
```

## 网络要求

如果您使用的是云端 API（如 OpenAI 或 Cloudflare），请确保您的服务器或设备能够访问这些 API 端点：

- OpenAI API: `https://api.openai.com`
- Cloudflare AI API: `https://api.cloudflare.com`

如果您所在的地区无法直接访问这些服务，您可能需要考虑使用代理或本地部署选项（如 Ollama）。

## 下一步

完成环境准备后，您可以继续[安装 YesImBot](installation.md)。
