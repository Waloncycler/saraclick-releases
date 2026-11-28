# M 芯片 Mac：首次安装与真实验收

适用于 SaraClick 1.5.3 RC1、另一台 Apple Silicon Mac。本轮使用 Claude、Codex、Antigravity，先验证小红书和 Instagram。本文是待执行的验收步骤，不是已完成的实机结果。

已有旧版用户：退出 SaraClick 并保留自己的数据目录，替换应用后重新启动；在 Edge/Chrome 扩展管理页重新加载原 SaraClick 扩展，确认版本 1.5.3，再刷新工作台重试小红书关联。无需卸载扩展或删除已有公众号、抖音等账号。本版包含 1.5.2 的小红书关联修复，解决了新用户没有账号记录就无法开始小红书绑定的问题，真实登录与发布仍按下列步骤核验。

## 1. 安装与打开

1. 在「关于本机」确认芯片为 Apple M 系列，macOS 为 13.5 或以上。使用已安装的 Chrome 或 Microsoft Edge。
2. 下载 [macos-arm64 安装包](https://github.com/Waloncycler/saraclick-releases/releases/download/v1.5.3-rc.1/SaraClick-1.5.3-macos-arm64.zip) 及 [SHA-256 校验文件](https://github.com/Waloncycler/saraclick-releases/releases/download/v1.5.3-rc.1/SaraClick-1.5.3-macos-arm64.zip.sha256)。包摘要：`2321a8d3cbd1f966e8a680ed104e1444749d9d24c520e78fcaa8e1f639cf1f5d`。
3. 解压后只有一个 SaraClick 图标是正常的：这是完整的 `SaraClick.app` 应用包，Finder 可能隐藏 `.app` 后缀，不会显示成普通文件夹。将它拖进「应用程序」再打开；升级用户先退出旧应用再替换，保留原数据目录。应用、Node 与 MCP 都是 arm64 原生，无需 Rosetta、Python 或 Node 安装。
4. 当前包尚未完成 Apple 公证。核对下载来源和 SHA-256 后，若提示「Apple 无法验证 SaraClick 是否包含可能危害 Mac 安全或泄露隐私的恶意软件」，先点弹窗中的「完成」，然后打开「系统设置 → 隐私与安全性」，向下滚动到「安全性」。macOS 27 先点「打开」，再点「仍要打开」；其他版本可能直接显示「仍要打开」，按系统要求确认身份，最后在再次出现的提示中点「打开」。**“仍要打开”在系统设置里，不在原来的弹窗里。** 详见 [Apple 官方步骤](https://support.apple.com/zh-cn/102445)。该入口通常只在尝试打开应用后约一小时内可用，参见 [Apple macOS 27 指南](https://support.apple.com/en-euro/guide/mac-help/mh40617/27/mac/27)。受管理电脑由其管理员处理。
5. 在 SaraClick 窗口选择 Chrome 或 Edge，点击「打开工作台」。全新安装应看到空账号列表，而不是他人的账号或任务。

如果个人 Mac 仍没有该入口，且确认下载来自本发布页、SHA-256 与上文一致，机主可以选择只移除 SaraClick 的下载隔离标记。先把应用放入「应用程序」，再在「终端」依次执行：

```sh
xattr -dr com.apple.quarantine "/Applications/SaraClick.app"
open "/Applications/SaraClick.app"
```

这只针对指定应用包，不关闭全机 Gatekeeper。提示找不到文件时核对应用位置；权限不足时保留报错，不盲目添加 sudo。系统明确报告检测到恶意软件或包损坏时，停止打开并核查下载包。

1.5.3 已统一扩展、网页和桌面图标，Mac 应用版本也显示为 1.5.3；这些改动不会替代 Apple 公证。当前无公证候选需要人工确认，顺畅直接分发仍需 Developer ID 签名和公证，无需上架 App Store。真实客户 Mac 首次启动和绑定仍待核验。

若这台电脑已有 SaraClick 或提示端口被占用，先核对现有安装；不要为验收删除资料、覆盖数据目录或随意结束占用进程。

通过标准：应用正常启动，所选浏览器可打开工作台，未导入任何其他人的业务数据。

## 更新后仍显示旧版本

先确认版本显示的位置。Edge/Chrome 扩展页的版本属于浏览器当前加载的扩展，不会因为只解压了新 ZIP 就自动更新。

退出正在运行的旧 SaraClick，将新 `SaraClick.app` 替换到「应用程序」，从这个位置打开。新应用启动后，点击「打开扩展安装目录」，核对其中 `manifest.json` 的 version 为 `1.5.3`。默认目录是 `~/Library/Application Support/SaraClick/extension`。

回到 `edge://extensions/` 或 `chrome://extensions/`，在**原 SaraClick 扩展卡片**点击重新加载，确认显示 1.5.3，再刷新工作台。若仍显示旧版，查看卡片「详情」里的加载路径：它可能仍指向旧下载目录。此时先记录原路径和卡片，不删除数据库、账号或浏览器资料，也不同时加载两份扩展。

Finder「显示简介」中，新包的应用版本同样应为 1.5.3。若新应用提示 8088/5174 端口占用，先退出原应用；不要随意结束不明进程。

## 2. 加载扩展并绑定自己的账号

1. 等 SaraClick 应用窗口出现后，点击「打开扩展安装目录」。真正的扩展文件夹在这里，里面有 `manifest.json`；默认位置为 `~/Library/Application Support/SaraClick/extension`，不要在 Edge 中选择 `SaraClick.app` 图标。
2. 在同一个浏览器打开 `chrome://extensions/` 或 `edge://extensions/`。新用户开启开发者模式并加载刚打开的目录；已有 SaraClick 扩展的升级用户重新加载原扩展。确认扩展版本为 1.5.3，再刷新工作台。
3. 返回工作台「账号与连接」，连接发布扩展，分别添加机主自己的小红书和 Instagram 账号。
4. 由账号主人在平台页面完成登录和必要验证，再回到 SaraClick 核对账号身份及发布设备。

通过标准：扩展已连接，目标账号的稳定身份已核实并属于机主，设备匹配。仅看到昵称、已登录页面或已安装扩展不算完成绑定。

## 3. 连接自己的 Agent，先做只读检查

在「连接自己的 AI Agent」创建测试授权：选本次账号、图文格式，仅勾选「创建待审核草稿」，测试额度可设为 3、有效期 1 天。确认后点击「创建 Agent 连接」，选择对应客户端，将一次性配置保存到自己 Agent 的私有配置。Claude（Desktop/Code）、Codex 和 Antigravity 分别创建授权，具体步骤见 [客户端接入说明](AGENT_CLIENTS.md)。三种客户端均先做本节只读检查和下一节草稿检查。

配置中的 command、args 和环境变量取自这台 Mac。不同 Agent 客户端的配置文件格式可能不同，应按实际客户端接入；不要把 JSON 直接当作所有客户端通用的配置文件。MCP 服务使用本机 stdio。

先给 Agent 这一段：

> 连接 SaraClick，只调用 saraclick_capabilities 和 saraclick_accounts。告诉我本次获准的账号、动作、格式，以及账号或扩展是否需要处理；不要上传素材、创建任务或发布。

通过标准：Agent 能读到这次授权的正确账号；权限仅包含所选动作。连接失败先保留错误信息，不通过重新创建发布任务解决。

## 4. 生成素材并创建一份待审核草稿

选一个你提供的测试主题，让自己的 Agent 使用自己的模型/生成工具制作正文和图片。将成品放入连接配置指定的 SARACLICK_MEDIA_ROOT，单文件不超过 20 MiB。

可给 Agent：

> 用我的生成工具完成这个测试主题的图文。保存成品到 SaraClick 配置的素材目录，上传图片，调用 preflight，使用一个本次新建且固定的 request_id，以 mode=draft 提交一次。返回 job_id、request_id 和当前状态供我检查。不要对外发布；请求超时先按原 request_id 查询，不换编号重发。

检查实际图片、正文、目标账号和草稿任务。保留 job_id/request_id。图片真正生成、上传成功、队列有草稿，分别核对。

通过标准：Agent 使用自己的模型生成的成品进入 SaraClick；任务处于待审核阶段，平台上没有因这一步出现新帖。

## 5. 单独验收自动发布及真实回执

待前四步通过后，由机主明确选定发布平台、账号和一篇允许公开的新内容。创建包含「自动对外发布」的新 Agent 授权并更新客户端连接配置，额度可限制为 1、有效期 1 天。旧草稿授权按需撤销；撤销会取消其尚未提交的任务。

每个平台先指定一个负责提交的客户端和一篇获准的新内容，同一篇不能交给多个 Agent 重复提交。只有实际测试的客户端×平台组合可标通过，六种组合的记录表见 [客户端接入说明](AGENT_CLIENTS.md)。

本阶段使用另一篇内容，与上一步草稿区分。不要把同一草稿换一个 request_id 重投来试图切换模式；若继续使用原草稿，应在原工作台确认原任务，这只验证主人确认后的执行，不能单独证明 Agent 的自动发布权限。

让 Agent 对这篇获准的新内容预检，以 mode=publish 提交一次，并每 3–5 秒查询原任务。保持 Mac 唤醒、SaraClick 和已配对浏览器运行。需要登录或验证码时由机主完成。

通过标准：平台上只有对应的获准作品；账号、正文、图片顺序及数量正确，SaraClick 和 Agent 返回对应作品链接/ID及核实后的状态。queued、claimed、submitting 不等于成功；unknown 或 needs_attention 应核对原任务，不能自动重发。

小红书需核对完整标题、正文、原生话题、图片数量/顺序、账号和真实作品回执。Instagram 需额外核对首次分享后的完整配文、换行、标签、图片顺序和裁切；如需人工补文，记录协助事实，不能算无协助通过。既有候选测试没有替代这一步。

## 6. 退出、重开及保留结果

在发布结果已明确后退出并重开 SaraClick 和 Agent，使用同一授权查询原 job_id，检查账号与原结果仍在。不要再次提交同一内容来测试恢复。

通过标准：重开后能查询同一任务和同一作品，未产生第二次发布。只有完成以上实际步骤，才记录本台机器、客户端和平台组合通过；不能外推到其他平台或 Windows。

## 反馈记录

记录测试 Mac 的芯片/macOS、浏览器及版本、Agent 及版本、平台、SaraClick/扩展版本、完成到第几步、原 job_id/request_id、脱敏错误文字，以及已确认的作品链接或草稿 ID。

只反馈相关错误和任务编号，不发送 MCP 配置中的凭据、平台密码、验证码、Cookie、完整数据库或浏览器数据。应用日志位于 `~/Library/Application Support/SaraClick/logs/desktop.log`；需要排错时只取相关且已脱敏的片段。

[完整 Agent 接口说明](AGENT_GUIDE.md) · [安装说明](README.md)
