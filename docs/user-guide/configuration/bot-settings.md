# 机器人设置

机器人设置是 YesImBot 配置中的基础部分，它定义了机器人的基本信息和行为特性。本章将详细介绍如何配置机器人的名称、人格、响应模式等基本设置。

## 基本设置

### 机器人名称

机器人名称是用户在对话中称呼机器人的名字。当用户在消息中提到这个名字时，机器人的回应意愿值会提高。

```yaml
BotSettings:
  BotName: "Athena"  # 机器人的名字
  BotNameAliases:     # 机器人名字的别名列表
    - "雅典娜"
    - "小雅"
```

**参数说明：**

- `BotName`：机器人的主要名称，建议使用简短、易记的名字
- `BotNameAliases`：机器人名字的别名列表，当用户使用这些别名时，机器人也会识别

### 人格设定

人格设定定义了机器人的性格特点和背景故事，影响机器人的回应风格和内容。

```yaml
BotSettings:
  Personality: "友好、知识渊博的AI助手，喜欢用简洁明了的语言解释复杂概念。"
  BackgroundStory: "Athena是一个设计用于群聊的AI助手，擅长回答问题、参与讨论和提供有用信息。"
```

**参数说明：**

- `Personality`：机器人的性格特点描述，影响回应的语气和风格
- `BackgroundStory`：机器人的背景故事，提供上下文信息，帮助语言模型理解其角色

## 响应设置

### 响应模式

响应模式控制机器人如何回应用户的消息。

```yaml
BotSettings:
  ResponseMode: "balanced"  # 响应模式：passive, balanced, active
  MaxResponseLength: 500    # 最大回应长度（字符数）
  MinResponseLength: 20     # 最小回应长度（字符数）
```

**参数说明：**

- `ResponseMode`：机器人的响应模式
  - `passive`：被动模式，主要在被直接提问或@时回应
  - `balanced`：平衡模式，在适当的时机参与对话（推荐）
  - `active`：主动模式，积极参与对话，回应频率较高
- `MaxResponseLength`：单条回应的最大字符数，防止回应过长
- `MinResponseLength`：单条回应的最小字符数，确保回应有足够的信息量

### 回应延迟

回应延迟设置可以模拟人类思考和打字的时间，使机器人的回应更加自然。

```yaml
BotSettings:
  EnableResponseDelay: true       # 是否启用回应延迟
  BaseResponseDelay: 2            # 基础延迟时间（秒）
  ResponseDelayPerChar: 0.05      # 每字符额外延迟时间（秒）
  MaxResponseDelay: 15            # 最大延迟时间（秒）
```

**参数说明：**

- `EnableResponseDelay`：是否启用回应延迟功能
- `BaseResponseDelay`：基础延迟时间，模拟思考时间
- `ResponseDelayPerChar`：每字符额外延迟时间，模拟打字速度
- `MaxResponseDelay`：最大延迟时间，防止长回应导致过长延迟

实际延迟时间计算公式：`min(BaseResponseDelay + ResponseLength * ResponseDelayPerChar, MaxResponseDelay)`

## 高级设置

### 情绪模拟

情绪模拟功能使机器人能够根据对话内容和上下文，表现出适当的情绪变化，使交流更加自然。

```yaml
BotSettings:
  EnableEmotionSimulation: true   # 是否启用情绪模拟
  EmotionChangeRate: 0.3          # 情绪变化速率（0-1）
  DefaultEmotion: "neutral"       # 默认情绪状态
```

**参数说明：**

- `EnableEmotionSimulation`：是否启用情绪模拟功能
- `EmotionChangeRate`：情绪变化的速率，值越大变化越快
- `DefaultEmotion`：默认的情绪状态，可选值包括：`happy`, `sad`, `angry`, `surprised`, `neutral`

### 学习能力

学习能力设置控制机器人从对话中学习新知识和适应群组风格的能力。

```yaml
BotSettings:
  EnableLearning: true            # 是否启用学习能力
  LearningRate: 0.2               # 学习速率（0-1）
  StyleAdaptation: true           # 是否适应群组对话风格
```

**参数说明：**

- `EnableLearning`：是否启用学习能力
- `LearningRate`：学习新知识的速率，值越大学习越快
- `StyleAdaptation`：是否自动适应群组的对话风格

## 配置示例

以下是一个完整的机器人设置配置示例：

```yaml
BotSettings:
  # 基本信息
  BotName: "Athena"
  BotNameAliases:
    - "雅典娜"
    - "小雅"
  Personality: "友好、知识渊博的AI助手，喜欢用简洁明了的语言解释复杂概念。"
  BackgroundStory: "Athena是一个设计用于群聊的AI助手，擅长回答问题、参与讨论和提供有用信息。"
  
  # 响应设置
  ResponseMode: "balanced"
  MaxResponseLength: 500
  MinResponseLength: 20
  EnableResponseDelay: true
  BaseResponseDelay: 2
  ResponseDelayPerChar: 0.05
  MaxResponseDelay: 15
  
  # 高级设置
  EnableEmotionSimulation: true
  EmotionChangeRate: 0.3
  DefaultEmotion: "neutral"
  EnableLearning: true
  LearningRate: 0.2
  StyleAdaptation: true
```

## 最佳实践

1. **名称选择**：选择一个简短、独特且易于识别的名字，避免使用常见词汇作为机器人名称
2. **人格一致性**：确保人格设定和背景故事保持一致，避免矛盾的描述
3. **响应长度**：根据群聊的活跃度和主题调整响应长度，活跃群聊适合较短回应，专业讨论群适合较长回应
4. **延迟设置**：模拟真实的思考和打字时间，但不要设置过长的延迟，以免影响用户体验
5. **定期调整**：根据用户反馈和实际使用情况，定期调整机器人设置

## 常见问题

### 机器人不回应自己的名字

如果机器人不回应当用户提到其名字时，可能是因为：

1. `BotName` 或 `BotNameAliases` 配置不正确
2. 意愿值系统的阈值设置过高

解决方案：
- 检查 `BotName` 和 `BotNameAliases` 配置
- 降低意愿值阈值或增加名字提及的意愿值提升

### 回应风格与设定不符

如果机器人的回应风格与人格设定不符，可能是因为：

1. 人格描述不够具体或存在矛盾
2. 提示词模板没有正确引用人格设定

解决方案：
- 修改人格描述，使其更加具体和一致
- 检查提示词模板，确保正确引用了人格设定

### 回应延迟过长

如果机器人的回应延迟过长，可能是因为：

1. `BaseResponseDelay` 或 `ResponseDelayPerChar` 设置过高
2. 回应内容过长，导致累积的延迟时间过长

解决方案：
- 降低 `BaseResponseDelay` 和 `ResponseDelayPerChar` 的值
- 调整 `MaxResponseDelay` 设置一个合理的上限
- 减小 `MaxResponseLength` 限制回应长度
