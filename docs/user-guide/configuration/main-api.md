# 主 API 配置

Athena 依赖大模型 API 来工作。Athena 配置中提供了 **LLM API 相关配置**，支持配置多个 API，使得 API 调用负载均衡。

**这些配置决定了 Bot 的发言。**

## 基本配置

在 **LLM API 相关配置** 中，Athena 提供了一个配置项 `API.APIList`。对于每一项，又有以下几种子项：

- `API.APIList.Enable`
- `API.APIList.APIType`
- `API.APIList.BaseURL`
- `API.APIList.UID`
- `API.APIList.APIKey`
- `API.APIList.AIModel`
- `API.APIList.Ability`

以及以下这些可能的配置项：

- `API.APIList.NUMA`
- `API.APIList.NumCtx`
- `API.APIList.NumBatch`
- `API.APIList.NumGPU`
- `API.APIList.MainGPU`
- `API.APIList.LowVRAM`
- `API.APIList.LogitsAll`
- `API.APIList.VocabOnly`
- `API.APIList.UseMMap`
- `API.APIList.UseMLock`
- `API.APIList.NumThread`

### 1. `API.APIList.APIType`

API 的类型。可选项：`OpenAI`、`Cloudflare`、`Ollama`、`Gemini` 以及 `Custom URL`。当选择 `OpenAI` 以及 `Custom` 时，使用 OpenAI 的 API 格式。

当选择 `OpenAI` 时，API 的请求地址为 `${BaseURL}/v1/chat/completions`，而当选择 `Custom` 时，API 的请求地址为 `${BaseURL}`。

此外，当选择 Cloudflare 时，API 的请求地址为 `${BaseURL}/accounts/${UID}/ai/run/${model}`。

### 2. `API.APIList.BaseURL`

API 的基础 URL。以 GPTGOD 平台为例:

- 当 `API.APIList.APIType` 选择 `OpenAI` 时，本项填写 `https://api.gptgod.online`；

- 当 `API.APIList.APIType` 选择 `Custom` 时，本项填写 `https://api.gptgod.online/v1/chat/completions`。

特别地，当使用 Cloudflare API 时，你的 APIType 只能选择 `Cloudflare`，并且此项只能填写 `https://api.cloudflare.com/client/v4`。

### 3. `API.APIList.UID`

当且仅当您使用 Cloudflare API 时填写本项。本项为您的 Cloudflare 用户 ID。详见 Cloudflare Workers AI 相关页面。

### 4. `API.APIList.APIKey`

此项填写您的 API Token / API Key。具体请见相应 API 提供商的说明。一般而言,它类似如下字符串：
```text
sk-XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
```

### 5. `API.APIList.AIModel`

AI 模型名称。例如 `llama-3.1-405b`，具体请见相应 API 提供商的说明。特别地，如果你使用的是 Cloudflare AI，此处的格式应类似如下字符串：`@cf/meta/llama-3-8b-instruct`。

### 6. `API.APIList.Ability`

模型支持的功能。可选：`原生工具调用`、`识图功能`、`结构化输出`、`流式输出`、`深度思考`和`对话前缀续写`。你应该尽可能地正确勾选模型支持的所有功能。具体查询该模型的文档或咨询API服务提供商。

#### 原生工具调用

有些模型对调用外部工具功能 (function-call) 有专门的微调，来让模型更准确地识别可调用的工具和生成调用这些工具所需要的参数，这样的模型支持原生工具调用。

对于不支持原生工具调用的模型，Athena 内置了一份 prompt 和相关的工具调用逻辑，以让不支持原生工具调用的模型也能够调用工具。

#### 识图功能

图文多模态模型 (multi-modal model) 本身就能够理解图片，而无需额外的图片描述服务。如果你使用的模型是图文多模态模型，那么你可以在图片查看器的相关配置 `ImageViewer.How` 中选择 `LLM API 自带的多模态能力` 以启用此多模态模型的原生识图功能。在这里勾选此项用于在 LLM API 负载均衡中标记此模型的能力，以便在轮到不支持识图功能的模型且图片查看器配置选择使用 `LLM API 自带的多模态能力` 时，Athena 能够自动选择使用图片描述服务。

#### 结构化输出

有些 API 支持 `response_format` 参数，用于强制 LLM 返回合法的 JSON 格式。如果你的 API 支持此参数，你可以在这里勾选 `结构化输出`，之后请求体中会包含此参数。勾选此项将使得 `LLMResponseFormat` 设置项内容被忽略。这将大幅减少 LLM 返回的消息无法解析的可能。

#### 流式输出

绝大部分 API 都支持流式输出。流式输出让用户在第一个 Token 生成时就可以开始看到生成的内容。但由于发送消息时，有些平台不支持修改消息内容，所以我们仍然要等到所有 Tokens 生成完毕后才会处理回复。也就是说，即使开启流式输出也不会让你更早看到生成的内容。提供此选项的原因是，某些 API 和模型只支持流式输出。

#### 深度思考

有些模型支持深度思考功能。这些模型在生成正式回复之前，会生成一段思维链内容。消息中的思维链内容在进入历史消息之前必须去除，因此如果你使用的模型支持深度思考，你必须勾选此项，并填写 `ReasoningStart` 和 `ReasoningEnd` 以指定思维链的开始和结束标记，让 Athena 能够正确地定位思维链内容并去除。对于OpenAI的o系列模型，你还可以指定`ReasoningEffort`来控制思维链的长度。

#### 对话前缀续写

这是一项 DeepSeek 的 Beta 功能。如果你使用的 API 支持对话前缀续写，你可以勾选此项并填写 `StartWith` 参数，LLM 将以此前缀开始回复内容。善用此功能可以实现的效果包括但不限于：强制模型使用 JSON 或 XML 格式回复、强制模型设置 `status` 为 `success`、强制模型深度思考等。详情请参考 [DeepSeek 文档](https://api-docs.deepseek.com/zh-cn/guides/chat_prefix_completion)。

### 其他配置

当选择自部署的 Ollama 服务时，可自定义更多配置，它们会在模型加载时被应用。详情请自行阅读 [llama.cpp](https://github.com/ggerganov/llama.cpp/blob/master/examples/main/README.md) 文档。

## 负载均衡

你可以配置多个 API，每个 API 遵循以上配置。Athena 会轮流使用 `API.APIList` 列表的每一个 API，以实现均衡化调用。