# 验证器配置

验证器是 YesImBot 的重要组件，用于检查和过滤机器人的回复，确保回复内容符合预期标准和规则。本章将详细介绍如何配置和使用验证器，以提高机器人回复的质量和安全性。

## 验证器概述

验证器在机器人生成回复后、发送给用户前进行检查，可以过滤不适当的内容、纠正格式问题、增强回复质量，或者根据特定规则修改回复内容。YesImBot 支持多种验证器类型，可以同时启用多个验证器，形成验证链。

## 基本配置

验证器的基本配置包括启用/禁用验证器和配置验证链：

```yaml
Verifier:
  Enabled: true                   # 是否启用验证器
  FailureAction: "regenerate"     # 验证失败时的操作：regenerate, modify, ignore
  MaxRetries: 3                   # 最大重试次数
  Validators:                     # 验证器列表
    - Type: "ContentFilter"
      Enabled: true
      Priority: 10
    - Type: "FormatCorrector"
      Enabled: true
      Priority: 5
```

### 基本参数

| 参数名 | 描述 | 默认值 | 可选值 |
|-------|------|-------|-------|
| `Enabled` | 是否启用验证器系统 | true | true, false |
| `FailureAction` | 验证失败时的操作 | "regenerate" | "regenerate", "modify", "ignore" |
| `MaxRetries` | 最大重试次数 | 3 | 1-10 |

### 失败操作

验证失败时，系统可以执行以下操作：

- **regenerate**：重新生成回复，直到通过验证或达到最大重试次数
- **modify**：使用验证器提供的修改建议修改回复
- **ignore**：忽略验证失败，继续使用原始回复

## 内置验证器

YesImBot 提供了多种内置验证器，每种验证器专注于特定的检查和过滤功能：

### 内容过滤器

内容过滤器用于检查回复中是否包含不适当的内容，如敏感词、有害信息等：

```yaml
Verifier:
  Validators:
    - Type: "ContentFilter"
      Enabled: true
      Priority: 10
      Settings:
        FilterLevel: "medium"     # 过滤级别：low, medium, high
        CustomWords:              # 自定义敏感词列表
          - "敏感词1"
          - "敏感词2"
        AllowedPatterns:          # 允许的模式（正则表达式）
          - "^特定允许的模式$"
        BlockedPatterns:          # 禁止的模式（正则表达式）
          - "^特定禁止的模式$"
```

#### 参数说明

| 参数名 | 描述 | 默认值 | 可选值/格式 |
|-------|------|-------|------------|
| `FilterLevel` | 过滤级别 | "medium" | "low", "medium", "high" |
| `CustomWords` | 自定义敏感词列表 | [] | 字符串数组 |
| `AllowedPatterns` | 允许的模式（正则表达式） | [] | 正则表达式数组 |
| `BlockedPatterns` | 禁止的模式（正则表达式） | [] | 正则表达式数组 |

### 格式校正器

格式校正器用于检查和修正回复的格式问题，如多余的空格、不一致的标点符号等：

```yaml
Verifier:
  Validators:
    - Type: "FormatCorrector"
      Enabled: true
      Priority: 5
      Settings:
        FixSpacing: true          # 修复空格问题
        FixPunctuation: true      # 修复标点符号问题
        FixCapitalization: true   # 修复大小写问题
        RemoveExcessiveNewlines: true  # 移除过多的换行
        MaxConsecutiveNewlines: 2  # 最大连续换行数
```

#### 参数说明

| 参数名 | 描述 | 默认值 | 可选值 |
|-------|------|-------|-------|
| `FixSpacing` | 修复空格问题 | true | true, false |
| `FixPunctuation` | 修复标点符号问题 | true | true, false |
| `FixCapitalization` | 修复大小写问题 | true | true, false |
| `RemoveExcessiveNewlines` | 移除过多的换行 | true | true, false |
| `MaxConsecutiveNewlines` | 最大连续换行数 | 2 | 1-5 |

### 长度控制器

长度控制器用于确保回复的长度在合理范围内：

```yaml
Verifier:
  Validators:
    - Type: "LengthController"
      Enabled: true
      Priority: 8
      Settings:
        MinLength: 20             # 最小长度（字符数）
        MaxLength: 500            # 最大长度（字符数）
        TruncationMethod: "smart" # 截断方法：simple, smart
        AddEllipsis: true         # 截断时是否添加省略号
```

#### 参数说明

| 参数名 | 描述 | 默认值 | 可选值 |
|-------|------|-------|-------|
| `MinLength` | 最小长度（字符数） | 20 | 1-100 |
| `MaxLength` | 最大长度（字符数） | 500 | 100-2000 |
| `TruncationMethod` | 截断方法 | "smart" | "simple", "smart" |
| `AddEllipsis` | 截断时是否添加省略号 | true | true, false |

### 语气调整器

语气调整器用于确保回复的语气符合预期：

```yaml
Verifier:
  Validators:
    - Type: "ToneAdjuster"
      Enabled: true
      Priority: 7
      Settings:
        TargetTone: "friendly"    # 目标语气：friendly, formal, casual, enthusiastic
        ForceEmoji: false         # 是否强制使用表情符号
        MaxEmojis: 2              # 最大表情符号数量
        MinPoliteness: 3          # 最小礼貌程度（1-5）
```

#### 参数说明

| 参数名 | 描述 | 默认值 | 可选值 |
|-------|------|-------|-------|
| `TargetTone` | 目标语气 | "friendly" | "friendly", "formal", "casual", "enthusiastic" |
| `ForceEmoji` | 是否强制使用表情符号 | false | true, false |
| `MaxEmojis` | 最大表情符号数量 | 2 | 0-10 |
| `MinPoliteness` | 最小礼貌程度（1-5） | 3 | 1-5 |

### 一致性检查器

一致性检查器用于确保回复与机器人的人格设定和之前的对话保持一致：

```yaml
Verifier:
  Validators:
    - Type: "ConsistencyChecker"
      Enabled: true
      Priority: 9
      Settings:
        CheckPersonality: true    # 检查是否与人格设定一致
        CheckFactual: true        # 检查事实一致性
        CheckContextual: true     # 检查上下文一致性
        StrictnessLevel: "medium" # 严格程度：low, medium, high
```

#### 参数说明

| 参数名 | 描述 | 默认值 | 可选值 |
|-------|------|-------|-------|
| `CheckPersonality` | 检查是否与人格设定一致 | true | true, false |
| `CheckFactual` | 检查事实一致性 | true | true, false |
| `CheckContextual` | 检查上下文一致性 | true | true, false |
| `StrictnessLevel` | 严格程度 | "medium" | "low", "medium", "high" |

## 自定义验证器

除了内置验证器外，YesImBot 还支持自定义验证器，您可以通过 JavaScript 或 Python 脚本创建自己的验证器：

```yaml
Verifier:
  Validators:
    - Type: "Custom"
      Enabled: true
      Priority: 15
      Settings:
        ScriptType: "js"          # 脚本类型：js, python
        ScriptPath: "/path/to/custom_validator.js"  # 脚本路径
        FunctionName: "validate"  # 函数名
        Timeout: 5000             # 超时时间（毫秒）
```

### 自定义验证器脚本

自定义验证器脚本需要实现一个特定的函数接口：

#### JavaScript 示例

```javascript
/**
 * 自定义验证器函数
 * @param {string} reply - 机器人的回复内容
 * @param {object} context - 上下文信息，包含用户消息、对话历史等
 * @returns {object} - 返回验证结果
 */
function validate(reply, context) {
  // 验证逻辑
  const isValid = /* 自定义验证逻辑 */;
  
  return {
    valid: isValid,  // 是否有效
    reason: isValid ? "" : "验证失败的原因",  // 失败原因
    suggestion: isValid ? "" : "修改建议"  // 修改建议
  };
}

module.exports = { validate };
```

#### Python 示例

```python
def validate(reply, context):
    """
    自定义验证器函数
    
    Args:
        reply (str): 机器人的回复内容
        context (dict): 上下文信息，包含用户消息、对话历史等
        
    Returns:
        dict: 验证结果
    """
    # 验证逻辑
    is_valid = # 自定义验证逻辑
    
    return {
        "valid": is_valid,  # 是否有效
        "reason": "" if is_valid else "验证失败的原因",  # 失败原因
        "suggestion": "" if is_valid else "修改建议"  # 修改建议
    }
```

## 验证链配置

验证链定义了多个验证器的执行顺序和组合方式：

```yaml
Verifier:
  ValidatorChain:
    ExecutionMode: "sequential"   # 执行模式：sequential, parallel
    StopOnFirstFailure: true      # 是否在第一个失败时停止
    CombineResults: false         # 是否合并所有验证器的结果
```

### 参数说明

| 参数名 | 描述 | 默认值 | 可选值 |
|-------|------|-------|-------|
| `ExecutionMode` | 验证器执行模式 | "sequential" | "sequential", "parallel" |
| `StopOnFirstFailure` | 是否在第一个失败时停止 | true | true, false |
| `CombineResults` | 是否合并所有验证器的结果 | false | true, false |

### 执行模式

- **sequential**：按优先级顺序依次执行验证器
- **parallel**：同时执行所有验证器，然后合并结果

## 配置示例

以下是一个完整的验证器配置示例：

```yaml
Verifier:
  Enabled: true
  FailureAction: "regenerate"
  MaxRetries: 3
  
  Validators:
    - Type: "ContentFilter"
      Enabled: true
      Priority: 10
      Settings:
        FilterLevel: "medium"
        CustomWords:
          - "敏感词1"
          - "敏感词2"
    
    - Type: "FormatCorrector"
      Enabled: true
      Priority: 5
      Settings:
        FixSpacing: true
        FixPunctuation: true
        FixCapitalization: true
        RemoveExcessiveNewlines: true
        MaxConsecutiveNewlines: 2
    
    - Type: "LengthController"
      Enabled: true
      Priority: 8
      Settings:
        MinLength: 20
        MaxLength: 500
        TruncationMethod: "smart"
        AddEllipsis: true
    
    - Type: "ToneAdjuster"
      Enabled: true
      Priority: 7
      Settings:
        TargetTone: "friendly"
        ForceEmoji: false
        MaxEmojis: 2
        MinPoliteness: 3
    
    - Type: "ConsistencyChecker"
      Enabled: true
      Priority: 9
      Settings:
        CheckPersonality: true
        CheckFactual: true
        CheckContextual: true
        StrictnessLevel: "medium"
  
  ValidatorChain:
    ExecutionMode: "sequential"
    StopOnFirstFailure: true
    CombineResults: false
```

## 最佳实践

1. **优先级设置**：将内容过滤器和一致性检查器设置为较高优先级，确保重要的检查先执行
2. **性能考虑**：启用过多验证器可能影响响应速度，根据实际需求选择必要的验证器
3. **逐步调整**：从基本验证器开始，根据实际效果逐步添加和调整
4. **自定义词表**：根据群聊的特性和规则，定制内容过滤器的敏感词表
5. **定期更新**：随着群聊的发展和变化，定期更新验证器配置
6. **测试验证**：在应用到生产环境前，先在测试环境中验证配置效果

## 常见问题

### 验证器过于严格

如果验证器过于严格，导致大多数回复被拒绝，可以尝试：

- 降低内容过滤器的 `FilterLevel`（"medium" 或 "low"）
- 减少自定义敏感词列表
- 降低一致性检查器的 `StrictnessLevel`（"low"）
- 增加长度控制器的 `MaxLength` 和减少 `MinLength`

### 验证器效率低下

如果验证过程耗时过长，可以尝试：

- 使用 `parallel` 执行模式
- 减少启用的验证器数量
- 设置 `StopOnFirstFailure` 为 true
- 优化自定义验证器脚本

### 验证结果不一致

如果验证结果不一致或不符合预期，可能是因为：

- 验证器之间存在冲突
- 优先级设置不合理
- 自定义验证器逻辑有问题

解决方案：
- 检查验证器的优先级设置
- 确保验证器之间不存在冲突的要求
- 测试和调试自定义验证器脚本
