# 常见问题 (FAQ)

**Q: 为什么我的机器人不说话？**
**A:** 这是最常见的问题。请按以下步骤排查：
1.  **检查唤醒配置:** 确认机器人响应的当前群组或私聊已在 `agentBehavior.arousal.allowedChannelGroups` 中正确配置。这是最常见的原因。
2.  **使用@提及:** 在群里 `@` 机器人，这是最强的唤醒信号。如果 `@` 了还不回复，说明模型服务或API Key配置可能有问题。
3.  **启用调试模式:** 将 `system.debug.enable` 设为 `true`，并将 `system.logging.level` 设为 `debug`。此时，每次收到消息，Koishi 控制台都会打印详细的意愿值计算过程，方便您诊断是哪个环节的分数不够。
4.  **检查模型服务:** 确认您的 `modelService` 配置正确，`apiKey` 有效，网络通畅。查看控制台是否有 API 报错。

**Q: 如何添加一个新的 AI 模型？**
**A:** 分三步：
1.  **添加至提供商:** 在 `modelService.providers` 中找到对应的提供商（或新建一个），在其 `models` 列表中加入你的新模型 `modelId`。
2.  **添加至模型组:** 在 `modelService.modelGroups` 中创建一个新的模型组，或者在你希望使用该模型的现有模型组的 `models` 列表中，添加 `{ providerName: '...', modelId: '...' }`。
3.  **分配任务:** 在 `modelService.task` 中，将 `chat` 或其他任务指向你刚才配置好的模型组名称。

**Q: v3 版本和旧版 (v2/Athena) 的配置完全不兼容吗？**
**A:** 是的，**完全不兼容**。v3 版本对所有核心系统（意愿、记忆、模型）和配置结构都进行了彻底重构。如果您从旧版本升级，必须重新配置。请参考本篇新文档，不要再使用旧的配置。

**Q: 我可以将机器人的人格/记忆备份吗？**
**A:** 可以。机器人的人格和核心记忆都存储在您于 `capabilities.memory.coreMemoryPath` 指定的目录下的 `.md` 文件中。您只需要备份这些文件即可。此外，可以开启 `capabilities.memory.backup.enabled` 来定时备份归档记忆。
