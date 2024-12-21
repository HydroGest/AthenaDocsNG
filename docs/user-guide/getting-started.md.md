
# 快速上手

本文档假设你已部署 Koishi 及相应协议端和适配器。如果你还没有安装 Koishi，请参考 [Koishi新人入门帖](https://forum.koishi.xyz/t/topic/556) 或 [Koishi 官方文档](https://koishi.chat/zh-CN/)。

如果你需要从 v1 迁移，请参阅 [从 V1 迁移](migrate-from-v1)。

如果你并非 Onebot 用户，推荐使用 onebot 适配器，参阅 [NapCat 官方文档](https://napneko.pages.dev/) 或 [LLOneBot 官方文档](https://llonebot.github.io/zh-CN/)。**永远不要使用 QQ 官方适配器！**

## 安装插件

前往 Koishi 控制台的插件市场，搜索 `yesimbot`，点击安装。

## 配置

Athena 最少只需要设置以下配置项即可工作：

### 记忆槽位设置

这决定了你的 Bot 的记忆分区。

- `MemorySlot.SlotContains` ：记忆槽位，每一个记忆槽位都可以填入一个或多个会话 ID（群号或 `private:私聊账号`），在一个槽位中的会话 ID 会共享上下文。你可以每行填入一个群号，Bot 将会在这些聊群中发言。

<!--stackedit_data:
eyJoaXN0b3J5IjpbLTM0NjkwODk5Ml19
-->