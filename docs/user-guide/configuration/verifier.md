# 验证器配置

验证器作用于从大模型获取回答之后、机器人发送群聊消息之前。目前，验证器的功能是检验 Bot 当前回复的内容是否和上一次回复的内容相重复。实践证明，在开启验证器之后，Bot 的回复质量有明显的提升。

## 启用验证器

验证器默认不开启，你需要到 **相似度验证器配置** 中手动开启配置项 `Verifier.Enabled` 来打开相似度验证器。

## 配置验证器 API

我们提供了两种验证器方案，通过设置 `Verifier.Type` 来决定方案的类型。

### LLM 方案

此方案的原理是在 Bot 发送消息之前，再调用一次大模型 API 来检查本次回复的内容是否符合我们给定的要求。因此验证器也需要配置大模型的 API。调用时使用的 prompt 内置在 Athena 中。

其中这几项的说明详见 [主 API 配置](main-api.html)：

- `Verifier.APIType`
- `Verifier.BaseURL`
- `Verifier.UID`
- `Verifier.APIKey`
- `Verifier.AIModel`

请注意，以上的 API 配置仅作用于验证器。

### Embedding 方案

此方案的原理是在 Bot 发送消息之前，使用 Embedding 模型向量化本次回复的内容与上一次回复的内容后，计算余弦相似度，并归一化得到语义相似度。因此验证器还需要配置一个 Embedding 模型。

启用此方案后，请确保设置了 Embedding 相关配置，详见 [Embedding 模型配置](Embedding)。

## 设置验证器

验证器还需要提供一个配置项 `Verifier.SimilarityThreshold`，这一项填入一个 $[0, 1]$ 之间的实数，表示最低可接受的相似度阈值。当本次 Bot 将要发送的消息与 Bot 上一次发送的消息的相似度低于此值时将不会发送。该相似度并非完全的文本相似度，而是**语义相似度**。这意味着，即使两次发言的表述不同，但意义接近也不会发送。

此外，你可以通过 `Verifier.Action` 来设置当 Bot 生成了相似度超过阈值的回复后的操作。你可以选择直接丢弃或重新生成。