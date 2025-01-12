# Prompt 配置

Prompt （预设）是 Athena 的重要组成部分。一个好的 Prompt 可以最大程度发挥模型的性能，因此，设置好 Prompt 是非常重要的。

在 **机器人设定** 配置中，我们提供了 `Bot.PromptFileURL` 列表。对于列表的每一项，你只需要填写 Prompt 文件的下载链接或者**本地文件名称**即可。

::: note
对于所有的本地 Prompt 文件，请你把自己的 Prompt 文件保存在 Koishi 运行目录（即 `koishi.yml` 所在的目录）下。暂不支持读取其他位置的 Prompt。
:::

## 下载预制 Prompt

Athena 提供了一系列预先设计好的 Prompt，开箱即用。它们被托管在 GitHub 仓库 [HydroGest/promptHosting](https://github.com/HydroGest/promptHosting) 中。该仓库的结构如下：

::: file-tree

- src 存储 Prompt `mdt` 文件的目录
  - thirdparty 存储第三方 Prompt `mdt` 文件的目录
    - maoniang.mdt
    - prompt-pallas.mdt
  - alter-next.mdt
  - prompt-legacy.mdt
  - prompt-next-short.mdt
  - prompt-next.mdt
- LICENSE
- README.md

:::

例如，要下载 `src/alter-next.mdt` 你只需在配置中的列表项里填写 `https://raw.githubusercontent.com/HydroGest/promptHosting/refs/heads/main/src/alter-next.mdt`。

::: tip
在中国大陆境内，访问 Github 相关服务是非常慢的。因此，你可以使用一些第三方的文件加速 & CDN 服务，例如 jsdelivr。在本例中，该文件的下载链接可以改成 `https://fastly.jsdelivr.net/gh/HydroGest/promptHosting@main/src/alter-next.mdt`。
:::

## 选择 Prompt

在填写完毕 Prompt 的下载链接后，请在配置项 `Bot.PromptFileSelected` 里填写你将要使用的 Prompt 的下标，从 0 开始。

## 更新 Prompt 

在每次启动时，Athena 会自动更新填写的 Prompt 文件。如果您修改了 Prompt 文件，或是不希望消耗时间更新 Prompt 文件，推荐您在 **插件设置** 配置里，禁用配置项 `Settings.UpdatePromptOnLoad`。