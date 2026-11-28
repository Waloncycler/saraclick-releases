# SaraClick 本地部署包

SaraClick 让你在自己的电脑绑定社交账号，并连接自己的 AI Agent：生成素材 → 上传到 SaraClick → 排期或发布 → 查询原任务结果。

此仓库只用于分发安装包及使用说明。产品源码保持私有，用户的账号、登录会话、凭据、历史任务和私人素材不会包含在安装包中。

[下载当前候选 1.5.5 RC5](https://github.com/Waloncycler/saraclick-releases/releases/tag/v1.5.5-rc.5) · [全部版本](https://github.com/Waloncycler/saraclick-releases/releases)

| 电脑 | 选择的包 |
| --- | --- |
| Windows 64 位（Intel/AMD） | windows-x64 |
| Mac M 系列 / Apple Silicon | macos-arm64（原生，不依赖 Rosetta） |
| Intel Mac | macos-x64 |

运行要求：macOS 13.5 或更高版本；Windows 10/11 x64。Mac 下限由随包运行时决定，参见 [Node 官方支持表](https://github.com/nodejs/node/blob/v24.x/BUILDING.md#platform-list)。本版没有 Windows ARM 原生包。

## 开始使用

1. 下载匹配系统的 ZIP，并核对同版 SHA-256 文件。Windows 解压整个文件夹运行 SaraClick.exe；Mac 解压后把 SaraClick.app 放入「应用程序」。
2. 启动应用，使用 Chrome 或 Edge 打开工作台。在应用中打开扩展目录，在浏览器扩展管理页开启开发者模式并「加载解压缩的扩展」。采用本地部署，无需应用商店。
3. 在「账号与连接」连接发布扩展，添加并登录自己的社交账号，核对身份与发布设备。
4. 创建 Agent 授权，选择账号、格式、权限、额度和有效期。选择 Claude Desktop / Claude Code、Codex 或 Antigravity，复制或下载对应的一次性配置，把生成素材放入指定目录。每个客户端单独授权。
5. 先试待审核草稿，核对后按需启用自动发布。Agent 保存 request_id 并查询原任务；结果未知时继续核对，不能换编号重发。

发布和排期执行期间请保持电脑唤醒、SaraClick 与浏览器运行。平台要求验证码或重新登录时，由账号主人处理。程序和 MCP 已打包，普通用户无需安装 Python 或 Node。

M 芯片 Mac 首次使用可按 [安装与真实验收清单](MAC_APPLE_SILICON_ACCEPTANCE.md) 逐步检查，并分别记录安装、绑定、Agent 草稿和真实发布结果。

## Mac 首次打开

M 系列 Mac 选择 macos-arm64；Intel Mac 选择 macos-x64。先将应用放入「应用程序」，再打开。当前候选包未进行 Apple 公证：如果系统提示无法验证开发者，核对下载来源和 SHA-256 后，可以按 [Apple 官方说明](https://support.apple.com/en-us/102445) 在「系统设置 → 隐私与安全性 → 安全性」中为此应用确认打开；macOS 27 先点「打开」，再点「仍要打开」。受公司管理的电脑由管理员处理。个人 Mac 没有该入口时，见 [详细首次打开步骤](MAC_APPLE_SILICON_ACCEPTANCE.md)。本版已更换品牌图标并修正 Mac 应用版本信息，这些变更不替代 Developer ID 签名和公证；公证用于直接分发也无需上架 App Store。

## Agent 接入

[Claude、Codex、Antigravity 配置与验收](AGENT_CLIENTS.md) · [接口和结果处理说明](AGENT_GUIDE.md)。Agent 可使用打包的 MCP，也可调用本机 HTTP API。模型和生成素材由你的 Agent 管理。当前接口接收的单个文件最多 20 MiB，大视频需先压缩；远端模型通过本机 Agent 客户端连接，不需要将本机端口暴露到公网。

## 数据和更新

Windows 数据目录：`%LOCALAPPDATA%/SaraClick`；Mac：`~/Library/Application Support/SaraClick`。个人数据与应用分开，升级时保留原目录，不给其他用户复制自己的数据或浏览器 profile。

1.5.5 起，在「账号与连接 → 版本与更新」检查新版后，点击「更新并重启」。程序自动选择架构、断点续传、验证发布签名、等待当前任务结束，再切换并恢复工作台连接。准备失败继续使用原版；新版启动失败或切换中断时自动恢复旧程序，数据库、账号、Agent 授权、素材与发布回执保留。结果 unknown 的发布任务会阻止切换，可点「暂不更新」继续核对原任务。

1.5.1–1.5.4 用户需要一次退出旧应用并安装本版，后续兼容更新无需手动替换文件。完整初装包名为 `SaraClick-*`；`SaraClickRuntime-*` 和 `release-manifest.json` 供更新器自动使用，用户不用手动解压。固定启动器的 Finder/Windows 文件信息可能保留初装版本，当前运行版本以工作台显示为准。

扩展从应用提供的固定目录加载。完整新文件安装后，沿用原权限与发布状态检查，等待空闲再切换；浏览器真实回应一致才显示版本一致。遇到旧版本或更新失败，按面板提示重试或核对安装来源，不删除账号、配对资料或原发布记录。此前加载临时下载目录的用户需要迁入固定目录并连接一次，不能把更换 ZIP 当作浏览器已经升级。

更新由内置公钥验证 Ed25519 发布签名；它不替代操作系统发布者证书。当前本地部署包尚无 Developer ID、公证或 Windows 发布者证书，首次打开可能出现操作系统确认提示。不同平台的真实发布验收范围和已知限制以具体 Release 说明为准；自动化构建通过不等于所有社交平台均已完成真实发帖验证。
