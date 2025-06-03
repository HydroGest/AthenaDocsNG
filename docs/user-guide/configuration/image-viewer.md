# 图像功能

YesImBot 提供了强大的图像处理功能，使机器人能够查看、分析和生成图像。本章将详细介绍如何配置和使用这些图像功能，以增强机器人的交互能力。

## 图像功能概述

YesImBot 的图像功能主要包括以下几个方面：

1. **图像查看**：机器人能够查看和理解用户发送的图像
2. **图像分析**：分析图像内容，提取关键信息
3. **图像生成**：根据文本描述生成相关图像
4. **图像编辑**：修改和编辑现有图像

这些功能使机器人能够处理多模态交互，不仅限于文本对话，还能理解和生成视觉内容。

## 基本配置

图像功能的基本配置包括启用/禁用各项功能和设置基本参数：

```yaml
ImageViewer:
  Enabled: true                   # 是否启用图像功能
  MaxImageSize: 10                # 最大图像大小（MB）
  SupportedFormats:               # 支持的图像格式
    - "jpg"
    - "jpeg"
    - "png"
    - "gif"
    - "webp"
  TimeoutSeconds: 30              # 图像处理超时时间（秒）
```

### 基本参数

| 参数名 | 描述 | 默认值 | 推荐范围 |
|-------|------|-------|---------|
| `Enabled` | 是否启用图像功能 | true | - |
| `MaxImageSize` | 最大图像大小（MB） | 10 | 5-20 |
| `SupportedFormats` | 支持的图像格式 | ["jpg", "jpeg", "png", "gif", "webp"] | - |
| `TimeoutSeconds` | 图像处理超时时间（秒） | 30 | 15-60 |

## 图像查看配置

图像查看功能使机器人能够"看到"用户发送的图像，并在对话中理解和引用这些图像：

```yaml
ImageViewer:
  Viewing:
    Enabled: true                 # 是否启用图像查看
    MaxImagesPerMessage: 5        # 每条消息最多处理的图像数
    IncludeImageDescriptionInContext: true  # 是否在上下文中包含图像描述
    DescriptionDetail: "medium"   # 描述详细程度：low, medium, high
```

### 参数说明

| 参数名 | 描述 | 默认值 | 可选值 |
|-------|------|-------|-------|
| `Enabled` | 是否启用图像查看 | true | true, false |
| `MaxImagesPerMessage` | 每条消息最多处理的图像数 | 5 | 1-10 |
| `IncludeImageDescriptionInContext` | 是否在上下文中包含图像描述 | true | true, false |
| `DescriptionDetail` | 描述详细程度 | "medium" | "low", "medium", "high" |

## 图像分析配置

图像分析功能使机器人能够分析图像内容，提取关键信息：

```yaml
ImageViewer:
  Analysis:
    Enabled: true                 # 是否启用图像分析
    AnalysisLevel: "standard"     # 分析级别：basic, standard, detailed
    DetectObjects: true           # 是否检测物体
    DetectText: true              # 是否检测文本
    DetectFaces: false            # 是否检测人脸
    DetectLabels: true            # 是否检测标签
    AnalysisProvider: "default"   # 分析提供商：default, custom
    CustomEndpoint: ""            # 自定义分析端点
```

### 参数说明

| 参数名 | 描述 | 默认值 | 可选值 |
|-------|------|-------|-------|
| `Enabled` | 是否启用图像分析 | true | true, false |
| `AnalysisLevel` | 分析级别 | "standard" | "basic", "standard", "detailed" |
| `DetectObjects` | 是否检测物体 | true | true, false |
| `DetectText` | 是否检测文本 | true | true, false |
| `DetectFaces` | 是否检测人脸 | false | true, false |
| `DetectLabels` | 是否检测标签 | true | true, false |
| `AnalysisProvider` | 分析提供商 | "default" | "default", "custom" |
| `CustomEndpoint` | 自定义分析端点 | "" | URL 字符串 |

## 图像生成配置

图像生成功能使机器人能够根据文本描述生成相关图像：

```yaml
ImageViewer:
  Generation:
    Enabled: true                 # 是否启用图像生成
    Provider: "openai"            # 生成提供商：openai, stability, custom
    Model: "dall-e-3"             # 使用的模型
    MaxGenerationsPerDay: 50      # 每日最大生成次数
    DefaultSize: "1024x1024"      # 默认图像大小
    DefaultQuality: "standard"    # 默认质量：standard, hd
    AllowUserGeneration: true     # 是否允许用户请求生成
    UserGenerationCommand: "生成图像"  # 用户生成命令
    UserGenerationCooldown: 300   # 用户生成冷却时间（秒）
    APIKey: "your_api_key"        # API 密钥
    CustomEndpoint: ""            # 自定义生成端点
```

### 参数说明

| 参数名 | 描述 | 默认值 | 可选值 |
|-------|------|-------|-------|
| `Enabled` | 是否启用图像生成 | true | true, false |
| `Provider` | 生成提供商 | "openai" | "openai", "stability", "custom" |
| `Model` | 使用的模型 | "dall-e-3" | 取决于提供商 |
| `MaxGenerationsPerDay` | 每日最大生成次数 | 50 | 1-1000 |
| `DefaultSize` | 默认图像大小 | "1024x1024" | "256x256", "512x512", "1024x1024", "1024x1792", "1792x1024" |
| `DefaultQuality` | 默认质量 | "standard" | "standard", "hd" |
| `AllowUserGeneration` | 是否允许用户请求生成 | true | true, false |
| `UserGenerationCommand` | 用户生成命令 | "生成图像" | 任意字符串 |
| `UserGenerationCooldown` | 用户生成冷却时间（秒） | 300 | 0-3600 |
| `APIKey` | API 密钥 | "your_api_key" | 有效的 API 密钥 |
| `CustomEndpoint` | 自定义生成端点 | "" | URL 字符串 |

## 图像编辑配置

图像编辑功能使机器人能够修改和编辑现有图像：

```yaml
ImageViewer:
  Editing:
    Enabled: true                 # 是否启用图像编辑
    Provider: "openai"            # 编辑提供商：openai, custom
    Model: "dall-e-3"             # 使用的模型
    MaxEditsPerDay: 30            # 每日最大编辑次数
    AllowUserEditing: true        # 是否允许用户请求编辑
    UserEditingCommand: "编辑图像"  # 用户编辑命令
    UserEditingCooldown: 300      # 用户编辑冷却时间（秒）
    APIKey: "your_api_key"        # API 密钥
    CustomEndpoint: ""            # 自定义编辑端点
```

### 参数说明

| 参数名 | 描述 | 默认值 | 可选值 |
|-------|------|-------|-------|
| `Enabled` | 是否启用图像编辑 | true | true, false |
| `Provider` | 编辑提供商 | "openai" | "openai", "custom" |
| `Model` | 使用的模型 | "dall-e-3" | 取决于提供商 |
| `MaxEditsPerDay` | 每日最大编辑次数 | 30 | 1-1000 |
| `AllowUserEditing` | 是否允许用户请求编辑 | true | true, false |
| `UserEditingCommand` | 用户编辑命令 | "编辑图像" | 任意字符串 |
| `UserEditingCooldown` | 用户编辑冷却时间（秒） | 300 | 0-3600 |
| `APIKey` | API 密钥 | "your_api_key" | 有效的 API 密钥 |
| `CustomEndpoint` | 自定义编辑端点 | "" | URL 字符串 |

## 权限控制

图像功能的权限控制允许您限制哪些用户或群组可以使用图像功能：

```yaml
ImageViewer:
  Permissions:
    AdminOnly:
      Generation: false           # 图像生成是否仅管理员可用
      Editing: true               # 图像编辑是否仅管理员可用
    AllowedUsers:                 # 允许使用图像功能的用户列表
      - "123456789"
    AllowedGroups:                # 允许使用图像功能的群组列表
      - "987654321"
    BlockedUsers:                 # 禁止使用图像功能的用户列表
      - "111222333"
    BlockedGroups:                # 禁止使用图像功能的群组列表
      - "444555666"
```

### 参数说明

| 参数名 | 描述 | 默认值 | 可选值 |
|-------|------|-------|-------|
| `AdminOnly.Generation` | 图像生成是否仅管理员可用 | false | true, false |
| `AdminOnly.Editing` | 图像编辑是否仅管理员可用 | true | true, false |
| `AllowedUsers` | 允许使用图像功能的用户列表 | [] | 用户 ID 数组 |
| `AllowedGroups` | 允许使用图像功能的群组列表 | [] | 群组 ID 数组 |
| `BlockedUsers` | 禁止使用图像功能的用户列表 | [] | 用户 ID 数组 |
| `BlockedGroups` | 禁止使用图像功能的群组列表 | [] | 群组 ID 数组 |

## 存储设置

图像功能的存储设置控制如何存储和管理图像文件：

```yaml
ImageViewer:
  Storage:
    SaveImages: true              # 是否保存图像
    StoragePath: "./images"       # 图像存储路径
    MaxStorageSize: 1024          # 最大存储大小（MB）
    CleanupOldImages: true        # 是否清理旧图像
    ImageRetentionDays: 30        # 图像保留天数
```

### 参数说明

| 参数名 | 描述 | 默认值 | 推荐范围 |
|-------|------|-------|---------|
| `SaveImages` | 是否保存图像 | true | - |
| `StoragePath` | 图像存储路径 | "./images" | 有效的文件路径 |
| `MaxStorageSize` | 最大存储大小（MB） | 1024 | 100-10000 |
| `CleanupOldImages` | 是否清理旧图像 | true | - |
| `ImageRetentionDays` | 图像保留天数 | 30 | 1-365 |

## 配置示例

以下是一个完整的图像功能配置示例：

```yaml
ImageViewer:
  Enabled: true
  MaxImageSize: 10
  SupportedFormats:
    - "jpg"
    - "jpeg"
    - "png"
    - "gif"
    - "webp"
  TimeoutSeconds: 30
  
  Viewing:
    Enabled: true
    MaxImagesPerMessage: 5
    IncludeImageDescriptionInContext: true
    DescriptionDetail: "medium"
  
  Analysis:
    Enabled: true
    AnalysisLevel: "standard"
    DetectObjects: true
    DetectText: true
    DetectFaces: false
    DetectLabels: true
    AnalysisProvider: "default"
  
  Generation:
    Enabled: true
    Provider: "openai"
    Model: "dall-e-3"
    MaxGenerationsPerDay: 50
    DefaultSize: "1024x1024"
    DefaultQuality: "standard"
    AllowUserGeneration: true
    UserGenerationCommand: "生成图像"
    UserGenerationCooldown: 300
    APIKey: "your_api_key"
  
  Editing:
    Enabled: true
    Provider: "openai"
    Model: "dall-e-3"
    MaxEditsPerDay: 30
    AllowUserEditing: true
    UserEditingCommand: "编辑图像"
    UserEditingCooldown: 300
    APIKey: "your_api_key"
  
  Permissions:
    AdminOnly:
      Generation: false
      Editing: true
    AllowedUsers:
      - "123456789"
    AllowedGroups:
      - "987654321"
    BlockedUsers:
      - "111222333"
    BlockedGroups:
      - "444555666"
  
  Storage:
    SaveImages: true
    StoragePath: "./images"
    MaxStorageSize: 1024
    CleanupOldImages: true
    ImageRetentionDays: 30
```

## 使用方法

### 图像查看

当用户发送图像时，机器人会自动查看并理解图像内容。图像描述会被添加到对话上下文中，使机器人能够在后续对话中引用这些图像。

例如，用户发送一张猫的图片，机器人会理解这是一张猫的图片，并能在后续对话中引用这只猫。

### 图像生成

用户可以通过特定命令请求机器人生成图像：

```
生成图像 一只橙色的猫坐在窗台上，看着窗外的雨
```

机器人会根据描述生成相应的图像，并发送到群聊中。

### 图像编辑

用户可以请求机器人编辑之前发送的图像：

```
编辑图像 把猫的颜色改成蓝色，并添加一个彩虹在背景中
```

机器人会编辑最近的图像，根据描述进行修改，并发送修改后的图像。

## 最佳实践

1. **资源管理**：根据服务器资源和使用频率，合理设置图像大小限制和每日生成/编辑次数
2. **权限控制**：使用权限设置限制图像功能的使用，避免滥用
3. **存储管理**：定期清理旧图像，避免占用过多存储空间
4. **API 密钥安全**：确保 API 密钥安全存储，不要在公开场合分享
5. **用户引导**：向用户提供清晰的使用说明，包括支持的命令和格式
6. **质量监控**：定期检查生成的图像质量，根据需要调整配置

## 常见问题

### 图像生成失败

如果图像生成失败，可能是因为：

1. API 密钥不正确或已过期
2. 达到了每日生成限制
3. 描述内容违反了提供商的内容政策
4. 网络连接问题

解决方案：
- 检查 API 密钥是否正确
- 确认是否达到了每日生成限制
- 修改描述内容，避免敏感或违规内容
- 检查网络连接

### 图像分析不准确

如果图像分析结果不准确，可能是因为：

1. 图像质量不佳
2. 分析级别设置过低
3. 使用的分析提供商能力有限

解决方案：
- 提高图像质量
- 增加分析级别（"detailed"）
- 尝试使用其他分析提供商

### 存储空间不足

如果遇到存储空间不足的问题，可以：

1. 减少 `MaxStorageSize`
2. 减少 `ImageRetentionDays`
3. 手动清理不需要的图像
4. 增加服务器存储空间
