# 基本配置

安装完成 YesImBot 后，您需要进行一些基本配置才能让它正常工作。本指南将帮助您完成最基本的配置，使 YesImBot 能够在您的环境中运行。

## 配置概述

YesImBot 的配置主要分为以下几个部分：

1. **记忆槽位配置**：定义机器人的记忆分区
2. **API 配置**：设置语言模型 API 的连接信息
3. **机器人设置**：配置机器人的基本信息和行为
4. **提示词设置**：自定义机器人的提示词模板
5. **参数设置**：调整机器人的各种参数

本指南将重点介绍前两项，这是让 YesImBot 能够基本运行的最低要求。其他高级配置将在[用户指南](../user-guide/configuration/index.md)中详细介绍。

## 记忆槽位配置

记忆槽位决定了您的机器人的记忆分区。在同一个槽位中的会话 ID 会共享上下文，使机器人能够在不同的对话中保持连贯性。

### 步骤 1: 打开配置界面

1. 在 Koishi 控制台中，导航到"插件配置"
2. 找到并点击 YesImBot 插件

### 步骤 2: 配置记忆槽位

在配置界面中，找到"记忆槽位"部分，添加您希望机器人参与的会话 ID：

```
MemorySlot.SlotContains[]: 
```

在这里，您可以每行填入一个群号或私聊账号（格式为 `private:账号`），例如：

```
123456789
987654321
private:123456789
```

这表示机器人将在群号为 123456789 和 987654321 的群聊中，以及与账号 123456789 的私聊中活动。

## API 配置

API 配置决定了您的机器人使用哪个语言模型 API 来生成回复。YesImBot 支持多种 API 类型，包括 OpenAI、Cloudflare 和 Ollama。

### 步骤 1: 准备 API 信息

根据您选择的 API 类型，准备以下信息：

- **API 类型**：OpenAI、Cloudflare 或 Ollama
- **基础 URL**：API 的基础 URL
- **API 密钥**：您的 API 访问密钥
- **模型名称**：您希望使用的模型名称
- **账户 ID**：（仅 Cloudflare 需要）您的 Cloudflare 账户 ID

### 步骤 2: 配置 API

在配置界面中，找到"API 配置"部分，添加至少一个 API 配置：

```
API.APIList[]:
```

点击"添加项目"，然后填写以下信息：

#### OpenAI 配置示例

```
APIType: OpenAI
BaseURL: https://api.openai.com
APIKey: 您的OpenAI密钥
AIModel: gpt-4o-mini
```

#### Cloudflare 配置示例

```
APIType: Cloudflare
BaseURL: https://api.cloudflare.com/client/v4
APIKey: 您的Cloudflare密钥
AIModel: @cf/meta/llama-3-8b-instruct
UID: 您的Cloudflare账户ID
```

#### Ollama 配置示例

```
APIType: Ollama
BaseURL: http://localhost:11434
AIModel: llama2
```

## 保存配置

完成上述配置后，点击页面底部的"保存"按钮保存您的配置。

## 验证配置

配置保存后，您可以通过以下步骤验证配置是否正确：

1. 在您配置的群聊或私聊中发送一条消息
2. 查看 Koishi 控制台的日志，应该能看到 YesImBot 处理消息的记录
3. 继续发送几条消息，直到机器人回应（根据意愿值系统，机器人可能不会立即回应每条消息）

如果机器人能够正常回应，说明基本配置已经成功。

## 常见配置问题

### API 连接失败

如果 API 连接失败，可能是因为：

- API 密钥不正确
- 基础 URL 不正确
- 网络连接问题
- API 服务不可用

解决方案：

1. 检查 API 密钥和基础 URL 是否正确
2. 确认网络连接正常
3. 检查 API 服务状态
4. 查看 Koishi 控制台日志，了解具体错误信息

### 机器人不回应

如果机器人不回应消息，可能是因为：

- 记忆槽位配置不正确
- 意愿值阈值设置过高
- API 调用失败
- 提示词配置问题

解决方案：

1. 确认记忆槽位中包含了正确的会话 ID
2. 尝试降低意愿值阈值或直接在消息中@机器人
3. 检查 API 配置和连接
4. 查看 Koishi 控制台日志，了解具体问题

## 下一步

完成基本配置后，您可以：

- 尝试[首次运行](first-run.md)机器人，了解基本使用方法
- 探索[高级配置](../user-guide/configuration/index.md)，进一步定制您的机器人
- 了解[核心功能](../features/index.md)，充分利用 YesImBot 的能力
