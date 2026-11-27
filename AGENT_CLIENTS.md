# Claude、Codex 与 Antigravity 接入

SaraClick 1.5.1 提供按客户端复制、下载连接配置。安装包包含本机 MCP 程序，M 系列 Mac 使用 arm64 包，Intel Mac 使用 x64 包，Windows 使用 windows-x64 包。以下是配置步骤，实际客户端连接和真实发布需在自己的电脑验收。

## 先在 SaraClick 创建独立授权

1. 启动 SaraClick，在同一个 Chrome/Edge 浏览器完成扩展配对、自己的小红书与 Instagram 账号登录及稳定身份核对。
2. 在「账号与连接 → 连接自己的 AI Agent」分别创建 Claude、Codex、Antigravity 授权。每份授权只选择本次账号，首次勾选「图文」「创建待审核草稿」，设置合适的额度和有效期。
3. 创建后选择实际客户端，复制或下载配置。配置已带这台电脑的真实 command、args、凭据和素材目录，不必安装 Python/Node，也不用手写用户名或转义 Windows 路径。
4. 同一份凭据只给一个客户端。各授权的素材、任务和结果独立；另一个 Agent 不能读取该授权的任务。要继续查询原任务，保留原授权，主人也可在工作台核对。撤销前先处理尚未结束的任务。

配置含凭据，请保存到自己的私有目录，不提交 Git、群聊或公开工单。下载文件须与已有配置合并，只添加/替换 saraclick，保留其他服务器和设置。已经保存的配置不会因隐藏凭据自动删除。

## 各客户端放在哪里

| 客户端 | SaraClick 中选择 | 保存与加载 |
| --- | --- | --- |
| Claude Desktop | Claude Desktop（JSON） | 设置 → Developer → Edit Config，合并 mcpServers.saraclick，保存后完全退出并重开 |
| Claude Code | Claude Code（JSON） | 配置保存在私有目录，使用下方 --mcp-config 命令启动，进入 /mcp 查看连接 |
| Codex App / CLI | Codex（TOML） | 合并到用户目录的 .codex/config.toml，替换已有 saraclick 段，重开客户端 |
| Antigravity | Antigravity（JSON） | MCP Servers → Manage MCP Servers → View raw config，合并 mcpServers.saraclick 后重新加载 |

Claude Desktop 的配置文件通常位于 Mac 的 `~/Library/Application Support/Claude/claude_desktop_config.json`，Windows 的 `%APPDATA%\Claude\claude_desktop_config.json`。使用客户端打开文件可避免放错目录。[本机 MCP 配置说明](https://modelcontextprotocol.io/docs/develop/connect-local-servers)

Claude Code 示例（将示例路径换成自己的文件位置，命令中不用粘贴凭据）：

```sh
claude --mcp-config "/绝对路径/saraclick-claude-code.json"
```

Windows PowerShell 同样将绝对路径放在双引号中，例如 `claude --mcp-config "C:\Users\你的用户名\私有配置\saraclick-claude-code.json"`。每次按此方式启动即可加载这份私有配置；`/mcp` 显示实际连接状态。它不需要放入项目的 `.mcp.json`。[Claude Code MCP 说明](https://code.claude.com/docs/en/mcp)

Codex 文件在 Mac 为 `~/.codex/config.toml`，Windows 为 `%USERPROFILE%\.codex\config.toml`。导出内容是 `[mcp_servers.saraclick]` 和对应 `env` 段，不能把 JSON 直接粘进去。CLI 可用 `codex mcp get saraclick` 检查已登记的配置，但这不等于工具调用成功。请勿分享含凭据的输出。[Codex MCP 说明](https://developers.openai.com/codex/mcp/)

Antigravity 以「View raw config」实际打开的位置为准。当前官方文档列出用户目录 `.gemini/config/mcp_config.json` 和工作区 `.agents/mcp_config.json`；这里优先使用自己的用户配置，避免把凭据写入共享项目。旧版本路径可能不同，勿只按网上旧路径新建一个无人读取的文件。[Antigravity MCP 说明](https://antigravity.google/docs/mcp/)

这些连接使用本机 **stdio**：客户端启动随包的 `saraclick-mcp` 程序。`http://127.0.0.1:8088/api/agent/v1` 是 REST API，不是可填入客户端「远程 MCP URL」的地址。本机客户端可以调用云端模型；纯云端 Agent 不能直接访问这台电脑的 localhost。

## 三个客户端分别做连接检查

对每个客户端分别发送：

> 只调用 saraclick_capabilities 和 saraclick_accounts，报告本次授权的账号、动作、格式及就绪状态。不要创建任务、上传或发布。连接错误请原样报告，不要要求我提供平台密码或 Cookie。

必须看到自己授权的账号。看到服务器名字或工具列表，只能说明配置/发现的一部分完成。客户端自己的工具确认、目录权限或组织管理限制仍需按实际提示处理。

## 素材生成与草稿检查

SaraClick 接收成品，不替代 Agent 的图片生成工具。确认该客户端自己的模型/图片工具可以生成图片，并能把成品写到 `SARACLICK_MEDIA_ROOT`。Claude Desktop 的云端附件或生成结果不会仅靠连接 SaraClick 就自动成为本机文件；需要该客户端已有的生成和本地保存能力。手工下载能验证上传，但不能记成自动生成与保存已通过。

分别给每个客户端一个不同的测试主题和固定 request_id：

> 用我自己的生成工具制作此主题的正文和图片，并保存到 SaraClick 素材目录。先确认授权账号，上传成品，调用 saraclick_preflight，以 mode=draft 提交一次。返回 job_id、request_id 和原任务状态。不要对外发布。请求超时先按原 request_id 查询，不能更换编号重投。

核对图片、正文、目标账号、素材目录和草稿任务。单素材最多 20 MiB，单任务合计 48 MiB。同一篇内容只交给一个 Agent；三个 Agent 不能靠不同凭据重复投递同一篇。

## 小红书与 Instagram 真实验收

按 [M 芯片 Mac 验收清单](https://github.com/Waloncycler/saraclick-releases/blob/master/MAC_APPLE_SILICON_ACCEPTANCE.md) 执行。先完成三种 Agent 的只读与草稿检查，再由机主为每个平台指定一篇不同于草稿练习的、允许公开的新内容，以及负责提交的唯一客户端和账号。

创建包含 publish 的独立授权，限定账号、格式、额度、到期时间；预检后仅提交一次，并持续查询原任务。不要用更换 request_id 的方式把旧草稿重投为 publish。若选择主人在工作台确认原草稿，记录为主人确认流程，不算 Agent 自动发布验收。

- 小红书：检查本人账号、完整标题/正文、原生话题、图片数量/顺序、真实作品链接与原任务回执。审核中或未核实状态不算公开发布通过。
- Instagram：检查首次分享后的完整配文、段落换行、标签、轮播数量/顺序和裁切，以及本人账号和对应作品链接。既有版本只证明过不发布预填；首发配文仍需这次新帖检验。如需同帖人工补文，记录为协助修正，不算无协助通过。
- unknown/needs_attention：查原任务或由主人处理，不重发、不替换已有回执。平台需要登录/验证码时由账号主人完成。

首轮可以每个平台各选一个客户端发布；只有实际完成的客户端×平台组合可标通过。要验证其余组合，使用之后另行获准的新内容。不要为凑齐表格额外发帖。

## 记录表（待执行）

| 客户端 | 平台 | 只读授权 | 自己生成并创建草稿 | 获准新帖及真实回执 | 重开查询原任务 |
| --- | --- | --- | --- | --- | --- |
| Claude（注明 Desktop/Code） | 小红书 | 待测 | 待测 | 待测 | 待测 |
| Claude（注明 Desktop/Code） | Instagram | 待测 | 待测 | 待测 | 待测 |
| Codex | 小红书 | 待测 | 待测 | 待测 | 待测 |
| Codex | Instagram | 待测 | 待测 | 待测 | 待测 |
| Antigravity | 小红书 | 待测 | 待测 | 待测 | 待测 |
| Antigravity | Instagram | 待测 | 待测 | 待测 | 待测 |

记录芯片/macOS、浏览器、客户端及实际运行的 SaraClick/扩展版本，保留 job_id/request_id 和脱敏错误。完成后用同一授权重开客户端、查询同一任务，确认同一作品且没有再次提交。配置导出、构建或自动化测试不能替代这些实机记录。
