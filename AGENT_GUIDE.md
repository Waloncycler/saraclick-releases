# 连接自己的 AI Agent

SaraClick 负责账号、发布队列和回执；你的 Agent 使用自己的模型生成素材。先启动 SaraClick，并在「账号与连接」完成扩展配对、账号登录和身份核对。

## 连接 MCP

在「账号与连接」创建 Agent 授权，选择账号、内容格式、草稿/预填/自动发布权限、24 小时额度和到期时间。复制只显示一次的 MCP 配置到你的 Agent 客户端。配置中已有本机可执行文件路径、凭据和素材目录；不同电脑的路径与凭据不同，不要共用。

MCP 客户端需支持本机 stdio 服务。安装包已经包含 saraclick-mcp，无需另装 Python 或 Node。远端大模型通过本机 Agent 客户端使用此工具。

可给 Agent 这样的任务：

> 先查看 SaraClick 授权账号和能力。用我自己的生成工具制作本次图文，将成品保存到 SARACLICK_MEDIA_ROOT，上传后预检。第一次只创建 draft 并返回任务编号让我审核。以后在我授予 publish 权限的范围内执行发布，持续查询原任务并报告真实回执。遇到结果未知时不要换编号重发。

## 一次任务的顺序

1. saraclick_capabilities 和 saraclick_accounts：确认授权及账号状态。
2. 自己的 Agent 生成素材，存入配置指定目录。
3. saraclick_upload_media：传 file_path，保存返回的 asset_id。
4. saraclick_preflight：传账号 ID、正文、素材 ID 和稳定的 request_id，预检不入队。
5. saraclick_submit：使用相同参数提交一次。draft 留待主人在工作台确认；prefill 填入平台草稿；publish 按已授予权限自动执行。
6. saraclick_get_job：每 3–5 秒查询原 job_id；排期任务可降低频率。响应丢失时使用原 request_id 找回任务。saraclick_list_jobs 可查看近期任务。

仅未开始的任务可以通过 saraclick_cancel_job 取消。撤销授权会取消尚未提交的任务，已提交或结果未知的记录保留供核对。

## 提交参数示例

```json
{
  "request_id": "campaign-20261127-post-001",
  "account_ids": ["YOUR_ACCOUNT_ID"],
  "format": "DYNAMIC",
  "mode": "draft",
  "scheduled_at": 0,
  "content": {
    "title": "今天的新作品",
    "text": "由自己的 Agent 生成并核对的正文",
    "tags": ["原创"],
    "images": ["UPLOADED_ASSET_ID"]
  }
}
```

format 支持 DYNAMIC、ARTICLE、VIDEO、PODCAST，实际可用范围以 capabilities 为准。scheduled_at 是 Unix 秒，0 表示立即。ARTICLE 可传 html、digest、cover，HTML 图片使用 `src="asset:<asset_id>"`；VIDEO/PODCAST 通过 video/audio 字段传素材编号。

单个素材最多 20 MiB，每任务合计 48 MiB，每 Agent 最多 200 MiB 或 1,000 项；支持 PNG/JPEG/WebP/GIF、MP4、MP3/WAV。较大视频先由 Agent 压缩。素材路径必须位于 SARACLICK_MEDIA_ROOT，不能读取任意本机文件。

## HTTP 接口

本机地址：`http://127.0.0.1:8088/api/agent/v1`。携带 `Authorization: Bearer <你的 Agent 凭据>`，写请求使用 application/json。不要将本机端口映射到公网。

| 请求 | 用途 |
| --- | --- |
| GET /capabilities、GET /accounts | 能力与授权账号 |
| POST /assets | 上传 {name, mime_type, base64} |
| POST /preflight、POST /jobs | 预检、提交 |
| GET /jobs/{job_id} | 查询原任务 |
| GET /jobs?request_id=... | 提交响应丢失后找回 |
| GET /jobs | 近期任务 |
| POST /jobs/{job_id}/cancel，body={} | 取消未开始的任务 |

## 结果与故障处理

published 表示执行器已核实回执；queued、claimed、submitting 仍在处理，不能报告成功。unknown 或 needs_attention 要核对原任务或让账号主人处理，不可更换 request_id 自动重发。相同 request_id 和相同参数返回原任务；同编号参数不同会拒绝。

登录过期、验证码、身份不一致、扩展离线时，主人在自己的浏览器处理，再核对原任务。权限失效时重新授权；额度不足时等待恢复或由主人调整。发布执行期间保持电脑唤醒、SaraClick 与配对浏览器运行。

公众号和 Spotify 目前以平台草稿回执为结果。其它平台的真实验收范围以 Release 说明为准。MCP/HTTP 的自动化测试不等于已验证所有平台的新账号真实发帖。本机 API 信任当前操作系统用户，不用于隔离恶意本机程序。
