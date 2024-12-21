# 从 V1 版本迁移

V2 版本几乎重构了整个插件，但边担心，从旧版本迁移并不困难。

## 先决条件

首先，确保你的版本是 V1 中最新的 `1.7.6` 版本。如果不是，请升级到此版本。

## 依赖

在 V2 中，我们新增了以下依赖：

- `async-mutex`
- `bson`

版本升级时，Koishi 不会自动帮你安装新增的依赖。因此请前往 Koishi 控制台中的 "依赖管理" 页面，点击右上角的加号手动输入以上依赖进行安装。

## 大功告成

至此，你的 Athena 迁移完毕，一起共同享受 V2 的新特性吧！

<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE1MzIxNjQ3MDldfQ==
-->