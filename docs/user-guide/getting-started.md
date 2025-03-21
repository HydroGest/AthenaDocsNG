# 快速上手

本文档假设你已部署 Koishi 及相应协议端和适配器。如果你还没有安装 Koishi，请参考 [Koishi新人入门帖](https://forum.koishi.xyz/t/topic/556) 或 [Koishi 官方文档](https://koishi.chat/zh-CN/)。

如果你需要从 v1 迁移，请参阅 [从 V1 迁移](migrate-from-v1.md)。

如果你并非 Onebot 用户，推荐使用 onebot 适配器，参阅 [NapCat 官方文档](https://napneko.pages.dev/) 或 [LLOneBot 官方文档](https://llonebot.github.io/zh-CN/)。

**目前我们暂不支持 QQ 官方适配器。**

## 安装插件

前往 Koishi 控制台的插件市场，搜索 `yesimbot`，点击安装。

## 配置

Athena 最少只需要设置以下配置项即可工作：

### 记忆槽位设置

这决定了你的 Bot 的记忆分区。

`MemorySlot.SlotContains[]` 
:   记忆槽位，每一个记忆槽位都可以填入一个或多个会话 ID（群号或 `private:私聊账号`），在一个槽位中的会话 ID 会共享上下文。你可以每行填入一个群号，Bot 将会在这些群聊中发言。

### LLM API 设置

这是你的 Bot 使用的主要大模型的 API 配置项。你的 Bot 发言主要与此配置中设置的模型有关。

`API.APIList[]` 是一个列表。你需要至少一个列表项。对于每一个列表项：

---

`APIType`
:   API 返回格式类型（适配器类型），可选 OpenAI / Cloudflare / Ollama。

`BaseURL`
:   API 基础 URL，若你是 `Custom` 类型，请填入 `https://api.openai.com/v1/chat/completions`；若你是 `Cloudflare`， 请填入 `https://api.cloudflare.com/client/v4`；如果是 `OpenAI` 类型，填入 `https://api.openai.com`。

`APIKey`
:   你的 API 密钥。

`AIModel`
:   模型名称，例如 `gpt-4o-mini`。

`UID`
:   当你是 `Cloudflare` 类型时才填写。 若你正在使用 Cloudflare，不要忘记这个配置。此处填 Cloudflare Account ID，若不清楚可以看看你 Cloudflare 控制台的 URL。

---

## 开始体验

现在，如果你的记忆槽位配置正确，那么如果你在你的设置过群聊中发言，插件设置页面将很快出现日志。继续发送消息，直到你的 Bot 回应。


<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEwNTQ2Mzg4NjZdfQ==
-->