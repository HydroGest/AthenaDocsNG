# 指令参考

YesImBot 提供了一系列指令，方便管理员进行配置和管理。所有指令都需要**管理员权限 (authority ≥ 3)** 才能使用。

## `setup` - 交互式配置向导

-   **功能:** 启动一个交互式的配置向导。这是**最推荐**的初次配置或重大修改的方式。
-   **用法:**
    ```
    setup
    ```
-   **描述:**
    该指令会触发一个引导流程，通过问答的形式帮助您完成最核心的配置，如设置模型提供商、创建模型组等。在流程中，您可以随时使用以下快捷指令控制进程：
    -   `/n` 或 `/next`: 跳到下一个配置项。
    -   `/p` 或 `/prev`: 返回上一个配置项。
    -   `/s` 或 `/save`: 保存当前进度并应用配置（会重载插件）。
    -   `/q` 或 `/quit`: 放弃所有更改并退出。

## `conf.get` - 查看配置项

-   **功能:** 查看当前的某个具体配置项的值。
-   **用法:**
    ```
    conf.get <key>
    ```
-   **参数:**
    -   `key` (string): 配置项的路径，使用点 `.` 分隔。支持数组索引 `[n]`。
-   **示例:**
    -   查看整个 `agentBehavior` 配置:
        ```
        conf.get agentBehavior
        ```
    -   查看响应意愿阈值:
        ```
        conf.get agentBehavior.willingness.lifecycle.probabilityThreshold
        ```
    -   查看第一个模型提供商的名称:
        ```
        conf.get modelService.providers[0].name
        ```
-   **返回:**
    配置项的值，以易于阅读的 YAML 格式返回。

## `conf.set` - 修改配置项

-   **功能:** 在运行时动态修改某个配置项的值。
-   **用法:**
    ```
    conf.set <key> <value>
    ```
-   **参数:**
    -   `key` (string): 要修改的配置项路径，同 `conf.get`。
    -   `value`: 要设置的新值。插件会自动尝试将输入解析为 `boolean`, `number` 或 `JSON`，如果都不匹配，则视为 `string`。
-   **示例:**
    -   启用视觉能力:
        ```
        conf.set agentBehavior.vision.enabled true
        ```
    -   修改发言精力惩罚:
        ```
        conf.set agentBehavior.willingness.lifecycle.replyCost 40
        ```
    -   修改唤醒关键词 (值为 JSON 数组):
        ```
        conf.set agentBehavior.willingness.interest.keywords '["学习", "文档", "帮助"]'
        ```
        !!! tip "值包含空格或特殊字符时，请使用引号将其括起来。"

-   **效果:**
    修改会立即在运行时生效。
    !!! warning "注意"
    使用 `conf.set` 进行的修改是**临时**的，不会自动保存到配置文件中。如果 Koishi 重启，这些修改将会丢失。如需永久保存，请在 Koishi 的配置页面手动保存一次。