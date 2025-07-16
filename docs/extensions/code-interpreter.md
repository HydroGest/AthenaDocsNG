# 扩展插件：代码解释器

**插件名:** `koishi-plugin-yesimbot-extension-code-interpreter`

代码解释器为 YesImBot 提供了一个强大的能力：在一个隔离、安全的沙箱环境中执行 JavaScript 代码。这使得 AI 可以完成计算、数据处理、调用轻量级 API 等复杂任务。

## 功能

-   **注册 AI 可用工具:** `execute_javascript(code: string)`
    -   **描述:** AI 可以调用此工具来执行任意 JS 代码。
    -   **输出:** 代码中通过 `console.log()` 输出的内容将作为工具的返回结果。
    -   **安全:** 代码在一个独立的 `worker_thread` 中运行，与主进程隔离。执行有严格的超时限制和模块访问白名单，确保安全。
-   **动态依赖安装:**
    -   代码中 `require()` 的模块如果不在本地，插件会自动使用您选择的包管理器（npm/yarn/bun）进行安装（仅限白名单内的模块）。

## 使用场景

-   **数学计算:** `"计算 Math.sqrt(144) * 12"` -> AI 调用 `execute_javascript` 执行 `console.log(Math.sqrt(144) * 12)`
-   **数据处理:** 解析 JSON，进行文本处理。
-   **调用无鉴权的 API:** 使用内置的 `https` 模块获取网络信息。

## 配置

在 `capabilities.tools.extra` 下找到 `code-interpreter` 键进行配置。

```yaml
capabilities:
  tools:
    extra:
      code-interpreter:
        timeout: 10000
        packageManager: npm
        allowedBuiltins:
          - os
          - path
          - util
        allowedModules:
          - 'axios' # 示例：允许 AI 使用 axios
        dependenciesPath: './.sandbox_modules'
        enableCache: true
        cacheTTL: 3600000
```

| 键 | 类型 | 默认值 | 描述 |
|---|---|---|---|
| `timeout` | number | `10000` | 代码执行的超时时间（毫秒）。 |
| `packageManager`| string| `npm` | 用于安装依赖的包管理器 (`npm`, `yarn`, `bun`)。 |
| `allowedBuiltins`| `string[]`| [...] | 允许在沙箱中使用的 Node.js 内置模块白名单。 |
| `allowedModules`| `string[]`| `[]` | 允许动态安装和使用的外部 npm 模块白名单。 |
| `dependenciesPath`| string | `./.sandbox_modules`| 动态安装的 npm 模块的存放路径。 |
| `enableCache` | boolean | `true` | 是否启用代码执行结果缓存。 |
| `cacheTTL`| number | `3600000`| 缓存的有效时间（毫秒）。 |