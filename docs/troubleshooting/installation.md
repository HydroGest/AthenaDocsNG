# 安装问题

在安装和部署 YesImBot 过程中，您可能会遇到各种问题。本章将帮助您识别和解决常见的安装问题，确保您能够顺利地开始使用 YesImBot。

## 前置依赖问题

### Node.js 版本不兼容

**症状**：安装过程中出现与 Node.js 版本相关的错误。

**解决方案**：
1. 检查您的 Node.js 版本：
   ```bash
   node -v
   ```
2. YesImBot 需要 Node.js 16.x 或更高版本。如果版本过低，请更新：
   ```bash
   # 使用 nvm 安装新版本（推荐）
   nvm install 18
   nvm use 18
   
   # 或直接从官网下载安装新版本
   # https://nodejs.org/
   ```
3. 更新后重新安装 YesImBot。

### Koishi 版本不兼容

**症状**：安装后出现 "插件与当前 Koishi 版本不兼容" 的错误。

**解决方案**：
1. 检查您的 Koishi 版本：
   ```bash
   koishi -v
   ```
2. YesImBot 需要 Koishi 4.12.0 或更高版本。如果版本过低，请更新：
   ```bash
   # 全局安装的 Koishi
   npm update -g koishi
   
   # 本地安装的 Koishi
   npm update koishi
   ```
3. 更新后重新安装 YesImBot。

### 缺少必要依赖

**症状**：安装过程中出现 "缺少依赖" 相关的错误。

**解决方案**：
1. 安装必要的系统依赖：
   ```bash
   # Debian/Ubuntu
   sudo apt-get update
   sudo apt-get install -y build-essential python3
   
   # CentOS/RHEL
   sudo yum install -y gcc-c++ make python3
   
   # Windows
   # 安装 Visual Studio Build Tools 和 Python
   ```
2. 安装必要的 npm 依赖：
   ```bash
   npm install -g node-gyp
   ```
3. 重新安装 YesImBot。

## 权限问题

### 文件权限不足

**症状**：安装过程中出现 "EACCES: permission denied" 错误。

**解决方案**：
1. 检查目录权限：
   ```bash
   ls -la ~/.koishi
   ```
2. 修复权限问题：
   ```bash
   # 修改目录所有权
   sudo chown -R $(whoami) ~/.koishi
   
   # 或修改目录权限
   chmod -R 755 ~/.koishi
   ```
3. 重新安装 YesImBot。

### npm 全局安装权限问题

**症状**：使用 npm 全局安装时出现权限错误。

**解决方案**：
1. 使用 sudo（不推荐）：
   ```bash
   sudo npm install -g koishi-plugin-yesimbot
   ```
2. 更改 npm 默认目录（推荐）：
   ```bash
   mkdir ~/.npm-global
   npm config set prefix '~/.npm-global'
   echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.profile
   source ~/.profile
   ```
3. 使用 nvm 管理 Node.js（推荐）：
   ```bash
   # 安装 nvm
   curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
   # 安装 Node.js
   nvm install 18
   # 安装 Koishi
   npm install -g koishi
   ```

## 网络问题

### 下载超时

**症状**：安装过程中出现 "ETIMEDOUT" 或 "timeout" 错误。

**解决方案**：
1. 检查网络连接。
2. 使用国内镜像源：
   ```bash
   # 设置 npm 镜像
   npm config set registry https://registry.npmmirror.com
   
   # 或使用 cnpm
   npm install -g cnpm --registry=https://registry.npmmirror.com
   cnpm install -g koishi-plugin-yesimbot
   ```
3. 增加超时时间：
   ```bash
   npm install --timeout=60000 -g koishi-plugin-yesimbot
   ```
4. 尝试使用代理：
   ```bash
   # 设置 HTTP 代理
   npm config set proxy http://proxy-server:port
   npm config set https-proxy http://proxy-server:port
   ```

### 防火墙或安全软件阻止

**症状**：安装过程中出现网络连接被拒绝的错误。

**解决方案**：
1. 临时禁用防火墙或安全软件。
2. 添加例外规则，允许 npm 和 Node.js 访问网络。
3. 如果在企业环境中，请联系网络管理员获取必要的访问权限。

## 安装过程问题

### 安装中断

**症状**：安装过程意外中断，导致安装不完整。

**解决方案**：
1. 清除可能的缓存：
   ```bash
   npm cache clean --force
   ```
2. 删除部分安装的文件：
   ```bash
   # 如果是全局安装
   rm -rf /usr/local/lib/node_modules/koishi-plugin-yesimbot
   # 如果是本地安装
   rm -rf ./node_modules/koishi-plugin-yesimbot
   ```
3. 重新安装 YesImBot。

### 依赖冲突

**症状**：安装过程中出现 "dependency conflict" 错误。

**解决方案**：
1. 检查依赖冲突：
   ```bash
   npm ls koishi-plugin-yesimbot
   ```
2. 尝试强制安装：
   ```bash
   npm install -g koishi-plugin-yesimbot --force
   ```
3. 如果问题仍然存在，可以尝试在新的目录中创建一个干净的 Koishi 实例：
   ```bash
   mkdir fresh-koishi && cd fresh-koishi
   npm init koishi
   # 按照提示完成初始化
   # 然后在新实例中安装 YesImBot
   ```

## 配置问题

### 配置文件损坏

**症状**：安装后无法启动，报告配置文件错误。

**解决方案**：
1. 检查配置文件：
   ```bash
   cat ~/.koishi/koishi.yml
   ```
2. 如果配置文件损坏，可以备份后重新创建：
   ```bash
   mv ~/.koishi/koishi.yml ~/.koishi/koishi.yml.bak
   koishi init
   ```
3. 然后重新配置 YesImBot。

### 配置路径错误

**症状**：YesImBot 无法找到配置文件或配置目录。

**解决方案**：
1. 确认 Koishi 配置目录位置：
   ```bash
   koishi config
   ```
2. 确保 YesImBot 的配置文件位于正确的目录中。
3. 如果使用自定义配置路径，确保在启动 Koishi 时指定了正确的路径：
   ```bash
   koishi start -c /path/to/config
   ```

## 平台特定问题

### Windows 特有问题

**症状**：在 Windows 上安装时遇到 Python 或编译相关错误。

**解决方案**：
1. 安装 Windows 构建工具：
   ```bash
   npm install --global --production windows-build-tools
   ```
2. 确保安装了 Python 2.7（某些依赖可能需要）：
   ```bash
   # 检查 Python 版本
   python --version
   # 如果需要，安装 Python 2.7
   ```
3. 使用管理员权限运行命令提示符或 PowerShell，然后重新安装。

### macOS 特有问题

**症状**：在 macOS 上安装时遇到 Xcode 或编译相关错误。

**解决方案**：
1. 安装 Xcode 命令行工具：
   ```bash
   xcode-select --install
   ```
2. 如果使用 M1/M2 芯片的 Mac，确保使用兼容的 Node.js 版本：
   ```bash
   # 使用 nvm 安装 ARM 版本的 Node.js
   arch -arm64 bash
   nvm install 18
   ```
3. 重新安装 YesImBot。

### Linux 特有问题

**症状**：在 Linux 上安装时遇到库依赖错误。

**解决方案**：
1. 安装必要的系统库：
   ```bash
   # Debian/Ubuntu
   sudo apt-get update
   sudo apt-get install -y libssl-dev libffi-dev python3-dev
   
   # CentOS/RHEL
   sudo yum install -y openssl-devel libffi-devel python3-devel
   ```
2. 重新安装 YesImBot。

## Docker 安装问题

### 容器启动失败

**症状**：Docker 容器无法启动或启动后立即退出。

**解决方案**：
1. 检查 Docker 日志：
   ```bash
   docker logs koishi-yesimbot
   ```
2. 确保挂载了正确的配置目录：
   ```bash
   docker run -d --name koishi-yesimbot \
     -v /path/to/config:/app/config \
     -p 5140:5140 \
     koishi/koishi
   ```
3. 检查配置文件权限：
   ```bash
   chmod -R 755 /path/to/config
   ```

### 网络问题

**症状**：Docker 容器无法连接到外部网络。

**解决方案**：
1. 检查 Docker 网络设置：
   ```bash
   docker network ls
   ```
2. 确保容器使用正确的网络：
   ```bash
   docker run --network host ...
   ```
3. 如果需要代理，可以在 Docker 运行时设置环境变量：
   ```bash
   docker run -e HTTP_PROXY=http://proxy:port -e HTTPS_PROXY=http://proxy:port ...
   ```

## 升级问题

### 升级后功能丢失

**症状**：升级 YesImBot 后，某些功能不再工作。

**解决方案**：
1. 检查版本兼容性，确保所有依赖都已更新到兼容版本。
2. 检查配置文件是否需要更新：
   ```bash
   # 备份旧配置
   cp ~/.koishi/koishi.yml ~/.koishi/koishi.yml.bak
   
   # 生成新的默认配置进行对比
   koishi init --default > ~/.koishi/koishi.default.yml
   
   # 比较差异
   diff ~/.koishi/koishi.yml.bak ~/.koishi/koishi.default.yml
   ```
3. 根据差异更新配置文件。

### 降级问题

**症状**：需要降级到旧版本，但遇到兼容性问题。

**解决方案**：
1. 卸载当前版本：
   ```bash
   npm uninstall -g koishi-plugin-yesimbot
   ```
2. 安装特定版本：
   ```bash
   npm install -g koishi-plugin-yesimbot@x.y.z
   ```
3. 恢复与该版本兼容的配置文件。

## 其他常见问题

### 内存不足

**症状**：安装或运行时出现 "JavaScript heap out of memory" 错误。

**解决方案**：
1. 增加 Node.js 可用内存：
   ```bash
   # 临时增加
   NODE_OPTIONS=--max_old_space_size=4096 npm install -g koishi-plugin-yesimbot
   
   # 永久增加（添加到 .bashrc 或 .zshrc）
   echo 'export NODE_OPTIONS=--max_old_space_size=4096' >> ~/.bashrc
   source ~/.bashrc
   ```
2. 如果在低内存设备上运行，考虑减少其他服务的内存使用或升级硬件。

### 磁盘空间不足

**症状**：安装过程中出现 "ENOSPC: no space left on device" 错误。

**解决方案**：
1. 检查磁盘空间：
   ```bash
   df -h
   ```
2. 清理不必要的文件：
   ```bash
   # 清理 npm 缓存
   npm cache clean --force
   
   # 清理系统缓存（Linux）
   sudo apt-get clean   # Debian/Ubuntu
   sudo yum clean all   # CentOS/RHEL
   ```
3. 如果需要，增加磁盘空间。

### 日志文件过大

**症状**：安装目录占用空间过大，主要是日志文件。

**解决方案**：
1. 检查日志文件大小：
   ```bash
   du -sh ~/.koishi/logs/*
   ```
2. 清理旧日志：
   ```bash
   find ~/.koishi/logs -name "*.log" -type f -mtime +7 -delete
   ```
3. 配置日志轮转，防止单个日志文件过大：
   ```yaml
   # 在 koishi.yml 中配置
   logger:
     maxDays: 7  # 保留最近 7 天的日志
   ```

## 获取帮助

如果您尝试了上述解决方案后仍然遇到问题，可以通过以下渠道获取帮助：

1. **查阅文档**：检查 [YesImBot 文档](https://github.com/HydroGest/YesImBot)，查看是否有更新的解决方案。
2. **提交 Issue**：在 [GitHub Issues](https://github.com/HydroGest/YesImBot/issues) 提交问题报告。
3. **加入社区**：加入 [QQ 交流群](https://qm.qq.com/cgi-bin/qm/qr?k=857518324) 或 [Discord 服务器](https://discord.gg/yesimbot) 寻求帮助。
4. **收集信息**：寻求帮助时，请提供以下信息：
   - YesImBot 版本
   - Koishi 版本
   - Node.js 版本
   - 操作系统类型和版本
   - 错误信息和日志
   - 复现问题的步骤
