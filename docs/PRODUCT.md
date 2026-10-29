# CATCHZMAIL 产品决策

更新于 2026-10-29。

## 已确定
- **用户**：工作室内部先用，按对外产品的标准设计（多账号、可扩展），之后对外发布。
- **平台**：桌面端优先（macOS / Windows），Tauri 2 + React + TypeScript。
- **邮件架构**：客户端连接已有邮箱（IMAP/SMTP，Gmail/Outlook 走 OAuth）。品牌域名邮箱后续用托管收发服务叠加。不自建邮件服务器。
- **核心差异点**：AI 行动收件箱。

## AI 行动收件箱
每封新邮件由 AI 归入四类之一：
| 类别 | 含义 | 首页表现 |
|---|---|---|
| 要回复 | 对方在等我回信 | 置顶显示，附一句话摘要和「起草回复」按钮 |
| 要处理 | 需要我做事但不一定回信（付款、审批、文件） | 显示，附截止时间（若识别到） |
| 仅知悉 | 抄送、进度同步 | 折叠进每日摘要 |
| 通知订阅 | 系统通知、营销、newsletter | 折叠进每日摘要 |

用户可一键改类，改动记录下来作为后续分类的少样本示例。

## 技术约定
- 前端：React + TypeScript + Vite，状态管理 Zustand，样式 Tailwind。
- 后端（Tauri Rust 侧）：IMAP 用 `async-imap`，SMTP 用 `lettre`，邮件解析 `mail-parser`。
- 存储：本地 SQLite（`rusqlite` 或 `sqlx`），全文搜索 FTS5。凭据存系统钥匙串（`keyring`）。
- AI：通过 Anthropic Messages API 调用 Claude，API Key 由用户在设置中填写，存钥匙串。
EOF
