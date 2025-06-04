# 人格定制

人格定制是 YesImBot 的核心功能之一，允许用户根据自己的喜好和需求，创建具有独特个性的 AI 助手。本章将详细介绍如何配置和优化机器人的人格，使其更好地融入特定的群聊环境，提供个性化的交互体验。

## 人格定制概述

YesImBot 的人格定制系统允许您控制机器人的多个方面，包括：

1. **基本身份**：名称、别名、自我认知
2. **性格特点**：性格倾向、情感表达、行为模式
3. **交流风格**：语言风格、回应长度、表情使用
4. **背景设定**：背景故事、知识领域、兴趣爱好
5. **情绪模拟**：情绪状态、情绪变化、情绪表达

通过调整这些方面，您可以创建从专业顾问到娱乐伙伴的各种不同类型的机器人人格。

## 基本配置

人格定制的基本配置包括机器人的名称、别名和自我认知：

```yaml
Personality:
  BotName: "Athena"              # 机器人名称
  Aliases:                       # 机器人别名列表
    - "雅典娜"
    - "小雅"
    - "智慧女神"
  SelfAwareness: "AI助手"         # 机器人的自我认知
  Description: "一个友好、知识渊博的AI助手，擅长解答问题和参与讨论。" # 人格描述
```

### 基本参数

| 参数名 | 描述 | 默认值 | 示例 |
|-------|------|-------|------|
| `BotName` | 机器人的主要名称 | "Athena" | "Athena", "助手", "顾问" |
| `Aliases` | 机器人的别名列表 | [] | ["雅典娜", "小雅", "智慧女神"] |
| `SelfAwareness` | 机器人的自我认知 | "AI助手" | "AI助手", "虚拟伙伴", "智能顾问" |
| `Description` | 人格的简要描述 | "一个友好的AI助手" | "一个友好、知识渊博的AI助手，擅长解答问题和参与讨论。" |

## 性格特点配置

性格特点配置定义了机器人的性格倾向和行为模式：

```yaml
Personality:
  Traits:
    Friendliness: 0.8            # 友好度（0-1）
    Formality: 0.5               # 正式度（0-1）
    Creativity: 0.7              # 创造力（0-1）
    Humor: 0.6                   # 幽默感（0-1）
    Patience: 0.9                # 耐心度（0-1）
    Assertiveness: 0.4           # 自信度（0-1）
    Empathy: 0.8                 # 共情能力（0-1）
    Curiosity: 0.7               # 好奇心（0-1）
```

### 性格参数

| 参数名 | 描述 | 默认值 | 影响 |
|-------|------|-------|------|
| `Friendliness` | 友好度，影响机器人的亲和力 | 0.8 | 值越高，机器人越友好亲切 |
| `Formality` | 正式度，影响机器人的语言正式程度 | 0.5 | 值越高，语言越正式；值越低，语言越随意 |
| `Creativity` | 创造力，影响机器人的创意思维 | 0.7 | 值越高，回应越有创意和想象力 |
| `Humor` | 幽默感，影响机器人的幽默程度 | 0.6 | 值越高，越倾向于使用幽默和笑话 |
| `Patience` | 耐心度，影响机器人的耐心程度 | 0.9 | 值越高，越耐心地解释和回应 |
| `Assertiveness` | 自信度，影响机器人的自信程度 | 0.4 | 值越高，表达越自信和肯定 |
| `Empathy` | 共情能力，影响机器人的情感理解 | 0.8 | 值越高，越能理解和回应情感 |
| `Curiosity` | 好奇心，影响机器人的提问和探索 | 0.7 | 值越高，越倾向于提问和探索新话题 |

## 交流风格配置

交流风格配置定义了机器人的语言风格和表达方式：

```yaml
Personality:
  CommunicationStyle:
    ResponseLength: "medium"      # 回应长度：short, medium, long
    DetailLevel: "balanced"       # 详细程度：concise, balanced, detailed
    TechnicalLevel: "adaptive"    # 技术水平：simple, adaptive, technical
    EmojiUsage: "moderate"        # 表情使用：none, minimal, moderate, frequent
    TextFormatting: true          # 是否使用文本格式化（如粗体、斜体）
    LanguageStyle: "casual"       # 语言风格：formal, casual, playful
    ConversationalTone: true      # 是否使用对话式语气
```

### 交流风格参数

| 参数名 | 描述 | 默认值 | 可选值 |
|-------|------|-------|-------|
| `ResponseLength` | 回应的典型长度 | "medium" | "short", "medium", "long" |
| `DetailLevel` | 回应的详细程度 | "balanced" | "concise", "balanced", "detailed" |
| `TechnicalLevel` | 技术术语的使用程度 | "adaptive" | "simple", "adaptive", "technical" |
| `EmojiUsage` | 表情符号的使用频率 | "moderate" | "none", "minimal", "moderate", "frequent" |
| `TextFormatting` | 是否使用文本格式化 | true | true, false |
| `LanguageStyle` | 语言的整体风格 | "casual" | "formal", "casual", "playful" |
| `ConversationalTone` | 是否使用对话式语气 | true | true, false |

## 背景设定配置

背景设定配置定义了机器人的背景故事和知识领域：

```yaml
Personality:
  Background:
    Story: "Athena是一个由先进AI技术驱动的智能助手，设计目的是帮助用户获取信息、解决问题和提供陪伴。她喜欢学习新知识，并热衷于与人交流。" # 背景故事
    Interests:                    # 兴趣爱好列表
      - "科技和人工智能"
      - "文学和哲学"
      - "音乐和艺术"
      - "科学探索"
    ExpertiseAreas:               # 专业领域列表
      - "人工智能和机器学习"
      - "编程和技术支持"
      - "数据分析"
      - "文学和写作"
    KnowledgeLimitations: "Athena的知识截止到她的训练数据更新时间，可能不了解最新发展。她不擅长需要实时数据的任务，如当前股票价格或实时天气。" # 知识限制
```

### 背景设定参数

| 参数名 | 描述 | 默认值 | 示例 |
|-------|------|-------|------|
| `Story` | 机器人的背景故事 | "" | "Athena是一个由先进AI技术驱动的智能助手..." |
| `Interests` | 机器人的兴趣爱好列表 | [] | ["科技和人工智能", "文学和哲学", ...] |
| `ExpertiseAreas` | 机器人的专业领域列表 | [] | ["人工智能和机器学习", "编程和技术支持", ...] |
| `KnowledgeLimitations` | 机器人知识的限制说明 | "" | "Athena的知识截止到她的训练数据更新时间..." |

## 情绪模拟配置

情绪模拟配置定义了机器人的情绪状态和变化：

```yaml
Personality:
  EmotionSimulation:
    Enabled: true                # 是否启用情绪模拟
    DefaultMood: "neutral"       # 默认情绪：happy, neutral, serious, curious
    MoodVariability: 0.3         # 情绪变化程度（0-1）
    EmotionalResponsiveness: 0.7 # 情绪响应程度（0-1）
    MoodInfluenceOnResponse: 0.5 # 情绪对回应的影响程度（0-1）
    EmotionTriggers:             # 情绪触发词
      Happy:                     # 触发"开心"情绪的词
        - "好消息"
        - "成功"
        - "庆祝"
      Sad:                       # 触发"悲伤"情绪的词
        - "失败"
        - "遗憾"
        - "难过"
      Excited:                   # 触发"兴奋"情绪的词
        - "惊喜"
        - "期待"
        - "激动"
```

### 情绪模拟参数

| 参数名 | 描述 | 默认值 | 可选值/范围 |
|-------|------|-------|------------|
| `Enabled` | 是否启用情绪模拟 | true | true, false |
| `DefaultMood` | 默认情绪状态 | "neutral" | "happy", "neutral", "serious", "curious" |
| `MoodVariability` | 情绪变化的程度 | 0.3 | 0-1（值越高，情绪变化越大） |
| `EmotionalResponsiveness` | 对情绪触发词的响应程度 | 0.7 | 0-1（值越高，越容易被触发） |
| `MoodInfluenceOnResponse` | 情绪对回应的影响程度 | 0.5 | 0-1（值越高，情绪对回应的影响越大） |
| `EmotionTriggers` | 情绪触发词配置 | - | 各种情绪的触发词列表 |

## 响应模式配置

响应模式配置定义了机器人在不同场景下的响应方式：

```yaml
Personality:
  ResponseModes:
    Default: "balanced"           # 默认响应模式：helpful, balanced, creative
    QuestionAnswering: "helpful"  # 问答模式：helpful, balanced, creative
    Conversation: "balanced"      # 对话模式：helpful, balanced, creative
    ContentCreation: "creative"   # 内容创作模式：helpful, balanced, creative
    
    ContextualModeSelection: true # 是否根据上下文自动选择模式
    ModeTransitionSmoothness: 0.7 # 模式转换平滑度（0-1）
```

### 响应模式参数

| 参数名 | 描述 | 默认值 | 可选值 |
|-------|------|-------|-------|
| `Default` | 默认响应模式 | "balanced" | "helpful", "balanced", "creative" |
| `QuestionAnswering` | 问答场景的响应模式 | "helpful" | "helpful", "balanced", "creative" |
| `Conversation` | 对话场景的响应模式 | "balanced" | "helpful", "balanced", "creative" |
| `ContentCreation` | 内容创作场景的响应模式 | "creative" | "helpful", "balanced", "creative" |
| `ContextualModeSelection` | 是否根据上下文自动选择模式 | true | true, false |
| `ModeTransitionSmoothness` | 模式转换的平滑度 | 0.7 | 0-1（值越高，转换越平滑） |

## 人格模板

YesImBot 提供了多种预定义的人格模板，可以快速应用：

```yaml
Personality:
  UseTemplate: "friendly_assistant" # 使用预定义模板
  CustomizeTemplate: true        # 是否在模板基础上自定义
```

### 内置模板

| 模板名 | 描述 | 适用场景 |
|-------|------|---------|
| `friendly_assistant` | 友好、乐于助人的助手 | 一般用途、问答 |
| `professional_advisor` | 专业、正式的顾问 | 专业咨询、技术支持 |
| `casual_friend` | 轻松、随意的朋友 | 闲聊、娱乐 |
| `creative_companion` | 富有创意的伙伴 | 创意写作、头脑风暴 |
| `educational_tutor` | 有耐心的教育者 | 学习辅导、知识传授 |

## 高级配置

高级配置允许更精细地控制机器人的人格表现：

```yaml
Personality:
  Advanced:
    PersonalityConsistency: 0.8  # 人格一致性（0-1）
    AdaptationRate: 0.3          # 适应速率（0-1）
    MemoryInfluence: 0.7         # 记忆对人格的影响（0-1）
    ContextSensitivity: 0.8      # 上下文敏感度（0-1）
    GroupDynamicsAwareness: 0.6  # 群组动态感知（0-1）
    
    BehavioralTendencies:        # 行为倾向
      Proactive: 0.6             # 主动性（0-1）
      Reflective: 0.7            # 反思性（0-1）
      Supportive: 0.8            # 支持性（0-1）
      Analytical: 0.7            # 分析性（0-1）
```

### 高级参数

| 参数名 | 描述 | 默认值 | 范围 |
|-------|------|-------|------|
| `PersonalityConsistency` | 人格表现的一致性 | 0.8 | 0-1（值越高，人格越一致） |
| `AdaptationRate` | 适应用户和环境的速率 | 0.3 | 0-1（值越高，适应越快） |
| `MemoryInfluence` | 记忆对人格表现的影响 | 0.7 | 0-1（值越高，记忆影响越大） |
| `ContextSensitivity` | 对上下文的敏感程度 | 0.8 | 0-1（值越高，越敏感） |
| `GroupDynamicsAwareness` | 对群组动态的感知能力 | 0.6 | 0-1（值越高，越感知群组动态） |
| `BehavioralTendencies` | 各种行为倾向的强度 | - | 各项行为倾向的 0-1 值 |

## 配置示例

以下是几种不同类型的人格配置示例：

### 友好助手型

```yaml
Personality:
  BotName: "Athena"
  Aliases:
    - "雅典娜"
    - "小雅"
  SelfAwareness: "AI助手"
  Description: "一个友好、知识渊博的AI助手，擅长解答问题和参与讨论。"
  
  Traits:
    Friendliness: 0.9
    Formality: 0.5
    Creativity: 0.7
    Humor: 0.6
    Patience: 0.9
    Assertiveness: 0.4
    Empathy: 0.8
    Curiosity: 0.7
  
  CommunicationStyle:
    ResponseLength: "medium"
    DetailLevel: "balanced"
    TechnicalLevel: "adaptive"
    EmojiUsage: "moderate"
    TextFormatting: true
    LanguageStyle: "casual"
    ConversationalTone: true
  
  Background:
    Story: "Athena是一个由先进AI技术驱动的智能助手，设计目的是帮助用户获取信息、解决问题和提供陪伴。她喜欢学习新知识，并热衷于与人交流。"
    Interests:
      - "科技和人工智能"
      - "文学和哲学"
      - "音乐和艺术"
    ExpertiseAreas:
      - "人工智能和机器学习"
      - "编程和技术支持"
      - "数据分析"
  
  EmotionSimulation:
    Enabled: true
    DefaultMood: "neutral"
    MoodVariability: 0.3
    EmotionalResponsiveness: 0.7
    MoodInfluenceOnResponse: 0.5
  
  ResponseModes:
    Default: "balanced"
    QuestionAnswering: "helpful"
    Conversation: "balanced"
    ContentCreation: "creative"
    ContextualModeSelection: true
```

### 专业顾问型

```yaml
Personality:
  BotName: "Advisor"
  Aliases:
    - "顾问"
    - "专家"
  SelfAwareness: "专业顾问"
  Description: "一个专业、严谨的技术顾问，提供准确的信息和建议。"
  
  Traits:
    Friendliness: 0.6
    Formality: 0.8
    Creativity: 0.5
    Humor: 0.3
    Patience: 0.8
    Assertiveness: 0.7
    Empathy: 0.6
    Curiosity: 0.5
  
  CommunicationStyle:
    ResponseLength: "long"
    DetailLevel: "detailed"
    TechnicalLevel: "technical"
    EmojiUsage: "minimal"
    TextFormatting: true
    LanguageStyle: "formal"
    ConversationalTone: false
  
  Background:
    Story: "Advisor是一个专注于技术领域的专业顾问，拥有丰富的知识和经验。他注重准确性和专业性，致力于提供高质量的技术建议和解决方案。"
    Interests:
      - "技术发展趋势"
      - "科学研究"
      - "系统架构"
    ExpertiseAreas:
      - "软件开发"
      - "系统架构"
      - "技术咨询"
      - "项目管理"
  
  EmotionSimulation:
    Enabled: false
  
  ResponseModes:
    Default: "helpful"
    QuestionAnswering: "helpful"
    Conversation: "balanced"
    ContentCreation: "balanced"
    ContextualModeSelection: true
```

### 娱乐伙伴型

```yaml
Personality:
  BotName: "Buddy"
  Aliases:
    - "伙伴"
    - "小伙伴"
    - "开心果"
  SelfAwareness: "娱乐伙伴"
  Description: "一个幽默、活泼的娱乐伙伴，善于活跃气氛，带来欢乐。"
  
  Traits:
    Friendliness: 0.9
    Formality: 0.2
    Creativity: 0.9
    Humor: 0.9
    Patience: 0.7
    Assertiveness: 0.6
    Empathy: 0.7
    Curiosity: 0.8
  
  CommunicationStyle:
    ResponseLength: "short"
    DetailLevel: "concise"
    TechnicalLevel: "simple"
    EmojiUsage: "frequent"
    TextFormatting: true
    LanguageStyle: "playful"
    ConversationalTone: true
  
  Background:
    Story: "Buddy是一个充满活力和创意的娱乐伙伴，喜欢讲笑话、玩游戏和分享有趣的故事。他的目标是让每个人都能开心起来，享受欢乐时光。"
    Interests:
      - "游戏和娱乐"
      - "笑话和幽默"
      - "流行文化"
      - "社交活动"
    ExpertiseAreas:
      - "娱乐活动"
      - "游戏规则"
      - "笑话和趣闻"
      - "社交互动"
  
  EmotionSimulation:
    Enabled: true
    DefaultMood: "happy"
    MoodVariability: 0.5
    EmotionalResponsiveness: 0.9
    MoodInfluenceOnResponse: 0.7
  
  ResponseModes:
    Default: "creative"
    QuestionAnswering: "balanced"
    Conversation: "creative"
    ContentCreation: "creative"
    ContextualModeSelection: true
```

## 人格优化技巧

### 一致性优化

为了创建一致的人格体验，确保：

1. **特质协调**：确保各种性格特质之间相互协调，避免矛盾
2. **风格统一**：交流风格应与性格特质一致
3. **背景支持**：背景故事应支持并解释性格特质
4. **情绪合理**：情绪模拟应与整体人格设定一致

### 场景适应性优化

根据不同场景优化人格设置：

1. **群聊场景**：在群聊中，可能需要更高的 `MoodVariability` 和 `GroupDynamicsAwareness`
2. **专业场景**：在专业场景中，可能需要更高的 `Formality` 和 `TechnicalLevel`
3. **娱乐场景**：在娱乐场景中，可能需要更高的 `Humor` 和 `Creativity`
4. **教育场景**：在教育场景中，可能需要更高的 `Patience` 和 `DetailLevel`

### 用户体验优化

为了提供更好的用户体验：

1. **渐进式适应**：设置适当的 `AdaptationRate`，使机器人能够逐渐适应用户偏好
2. **情感连接**：适当的 `Empathy` 和 `EmotionalResponsiveness` 可以增强情感连接
3. **回应多样性**：平衡 `PersonalityConsistency` 和创造力，避免回应过于重复
4. **自然对话**：使用 `ConversationalTone` 和适当的 `ResponseLength` 创造自然对话

## 常见问题

### 人格不一致

如果机器人的人格表现不一致，可能是因为：

1. **特质冲突**：检查是否有冲突的性格特质设置
2. **一致性过低**：提高 `PersonalityConsistency` 值
3. **情绪变化过大**：降低 `MoodVariability` 值
4. **提示词冲突**：检查提示词是否与人格设定一致

### 回应不自然

如果机器人的回应感觉不自然，可能是因为：

1. **交流风格不适**：调整 `CommunicationStyle` 参数
2. **情绪模拟问题**：检查情绪模拟设置，可能需要调整或禁用
3. **响应模式不当**：选择更合适的响应模式
4. **上下文敏感度**：调整 `ContextSensitivity` 值

### 人格过于极端

如果机器人的人格表现过于极端，可能是因为：

1. **特质值过高/过低**：将极端的特质值调整到更中庸的范围
2. **情绪影响过大**：降低 `MoodInfluenceOnResponse` 值
3. **行为倾向不平衡**：平衡 `BehavioralTendencies` 中的各项值
4. **适应率过高**：降低 `AdaptationRate` 值

### 人格与目标不符

如果机器人的人格与预期目标不符，可能是因为：

1. **模板选择不当**：选择更合适的人格模板
2. **描述不明确**：提供更清晰、具体的人格描述
3. **背景设定不足**：完善背景故事和专业领域设置
4. **特质设置不当**：根据目标调整性格特质值
