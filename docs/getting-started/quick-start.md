# 快速上手

安装并启用插件后，您需要进行一些关键配置，让 YesImBot 拥有对话的能力。本指南将引导您完成首次配置。

!!! danger "重要：启动必要条件"
    在开始配置前请注意，YesImBot 插件有两项**必须满足**的启动条件，否则插件将无法正常加载：
    1.  **配置可用模型：** 必须在 `modelService` 中至少配置一个能正常使用的 AI 模型。
    2.  **指定响应频道：** 必须在 `agentBehavior.arousal.allowedChannelGroups` 中至少添加一个允许 Bot 响应的频道。

我们**强烈推荐**使用交互式 `setup` 指令，这是最简单、最不容易出错的方式。

## 使用 `setup` 指令快速开始 (推荐)

在您的任意聊天窗口中，对机器人发送（需要管理员权限，通常为 authority ≥ 3）：

```
setup
```

机器人会回复一个验证码。正确输入后，配置向导将启动。请跟随向导的提示，至少完成以下三个核心步骤：

1.  **配置 AI 模型提供商:** 这是最关键的一步。您需要提供：
    -   **名称 (Name):** 自定义一个名字，如 `my_openai`。
    -   **类型 (Type):** 选择您的 API 类型（如 `OpenAI`, `Anthropic`, `Ollama` 等）。
    -   **API 密钥 (API Key):** 您的服务商提供的密钥。
    -   **API 地址 (Base URL):** (可选) 如果您使用代理或自建服务，请填写此项。

2.  **配置模型组 (Model Group):** 模型组用于管理模型和实现故障转移。
    -   将您刚刚创建的提供商中的某个具体模型（如 `gpt-4o`）添加到一个模型组中（如 `default_chat_group`）。

3.  **为任务分配模型组 (Task Assignment):** 告诉 YesImBot 在什么场景下使用哪个模型组。
    -   将 `chat` (聊天) 任务分配给您刚刚创建的模型组（`default_chat_group`）。

完成这三步并输入 `/save` 保存后，您的机器人就已经具备了基础的对话能力！

!!! tip "向导指令"
    在 `setup` 流程中，您可以使用以下指令：
    -   `/n` 或 `/next`: 前往下一项
    -   `/p` 或 `/prev`: 返回上一项
    -   `/s` 或 `/save`: 保存并应用配置
    -   `/q` 或 `/quit`: 放弃更改并退出

## 手动配置 (高级)

如果您偏好手动编辑 `koishi.yml` 或在 Web UI 的 YAML 编辑器中配置，以下是一个能让机器人工作的最小化配置。

**请务必将 `apiKey`、`modelId` 和 `id` (群号) 替换为您自己的信息。**

```yaml
# 在 koishi.yml 的 plugins 下
yesimbot:
  modelService:
    providers:
      - name: my_openai_provider
        enabled: true
        type: OpenAI
        apiKey: sk-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx # 你的 API Key
        models:
          - modelId: gpt-4-turbo # 你要使用的模型 ID
            abilities:
              - 对话
              - 函数调用
            parameters:
              temperature: 1.36
              topP: 0.8
              stream: true
    modelGroups:
      - name: default_group
        models:
          - providerName: my_openai_provider
            modelId: gpt-4-turbo
    task:
      chat: default_group
      embed: default_group
      summarize: default_group
  agentBehavior:
    arousal:
      allowedChannelGroups:
        - - platform: onebot # 允许响应的平台
            id: '123456789' # 允许响应的群号
```

## 验证配置

配置完成后，在您 `allowedChannelGroups` 中指定的群组里，尝试 `@` 机器人并向它问好：

```
@你的机器人 你好！
```

如果它能正常回复，说明您的基础配置已经成功！

## 首次运行问题排查

-   **机器人不回复:**
    -   确认已在 `agentBehavior.arousal.allowedChannelGroups` 中正确配置了当前群组。
    -   检查 `modelService.providers` 中的 `apiKey` 是否正确、有效。
    -   检查 Koishi 控制台日志，查看是否有 API 报错（如 401, 429）。
-   **`setup` 指令无反应:**
    -   确认发送指令的用户权限等级 ≥ 3。

!!! success "恭喜！"
    您已完成 YesImBot 的基础配置。现在，您可以开始深入探索它的 [核心概念](../concepts/agent-architecture.md) 或查阅完整的 [配置参考](../reference/configuration.md) 来进一步定制您的 AI 伙伴了。
