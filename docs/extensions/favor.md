# 扩展插件：好感度系统

**插件名:** `koishi-plugin-yesimbot-extension-favor`

好感度系统为 YesImBot 引入了与用户的情感连接。机器人可以根据对话内容，通过调用工具来增加或减少与特定用户的好感度，并将这一数值作为后续交互的参考。

## 功能

-   **注册 AI 可用工具:**
    -   `add_favor(user_id: string, amount: number)`: 为指定用户增加或减少好感度。
    -   `set_favor(user_id: string, amount: number)`: 直接设置用户的好感度数值。
-   **注入提示词片段:**
    -   该插件会自动向 AI 的提示词中注入两个片段，让 AI 了解当前的好感度状态：
    -   `{{roleplay.favor}}`: 包含当前用户好感度数值的文本。
    -   `{{roleplay.state}}`: 根据您配置的“好感度阶段”，返回对应的阶段描述文本。
-   **数据库集成:** 好感度数据会持久化存储在 Koishi 的数据库中。

## 使用场景

-   **情感交互:** AI 可以根据用户的友好程度，决定“内心OS”：`"用户对我表达了感谢，我应该增加对他的好感度" -> 调用 add_favor`。
-   **动态人格:** 结合提示词，实现“好感度越高，AI 说话越亲密”的动态效果。

## 配置

在 `capabilities.tools.extra` 下找到 `favor` 键进行配置。

```yaml
capabilities:
  tools:
    extra:
      favor:
        initialFavor: 20
        maxFavor: 100
        stage:
          - threshold: 0
            description: 陌生
          - threshold: 20
            description: 友好
          - threshold: 60
            description: 信赖
          - threshold: 90
            description: 挚友
```

| 键 | 类型 | 默认值 | 描述 |
|---|---|---|---|
| `initialFavor`| number | `20` | 新用户的初始好感度。 |
| `maxFavor` | number | `100` | 好感度的最大值。 |
| `stage` | `object[]`| `[]` | 好感度阶段配置。系统会从高到低匹配阈值。|