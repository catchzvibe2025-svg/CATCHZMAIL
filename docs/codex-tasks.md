# Codex 任务清单（MVP）

每个任务都可以单独交给 Codex 执行：`codex exec "$(cat docs/codex-tasks/<编号>.md)"`，或者把下面对应段落作为提示词。
每个任务完成标准：能编译、有基本测试、提交到当前分支。先读 `docs/PRODUCT.md`。

## T1 项目脚手架
用 Tauri 2 + React + TypeScript + Vite 初始化桌面应用，接入 Tailwind 和 Zustand。三栏布局：左侧账号与分类导航，中间邮件列表，右侧阅读区。放占位数据。配置 `pnpm tauri dev` 可运行，加 GitHub Actions 在 macOS 和 Windows 上构建。

## T2 账号与凭据
设置页可添加 IMAP/SMTP 账号（主机、端口、TLS、用户名、密码/授权码），预置 Gmail、Outlook、QQ、163 的服务器参数。密码存系统钥匙串（keyring crate），账号元数据存 SQLite。提供「测试连接」按钮。

## T3 邮件同步
Rust 侧用 async-imap 拉取 INBOX 最近 30 天邮件，mail-parser 解析，存入 SQLite（messages、threads、attachments 表），按 Message-ID / In-Reply-To / References 归并线程。支持 IDLE 或定时增量同步。通过 Tauri command 和事件把数据推给前端。

## T4 阅读与发送
阅读区安全渲染 HTML 邮件（沙箱 iframe，默认屏蔽远程图片），显示附件。写信窗口支持回复、全部回复、转发、附件，用 lettre 经 SMTP 发出，发送后写入已发送。

## T5 AI 行动分类
新邮件入库后调用 Claude（Messages API）分类为 要回复 / 要处理 / 仅知悉 / 通知订阅，并输出一句话摘要和识别到的截止时间（JSON 结构化输出）。结果存表。用户手动改类时记录，作为后续调用的少样本示例。API Key 在设置中填写，存钥匙串。无 Key 时退化为规则分类（发件人是否 noreply、List-Unsubscribe 头等）。

## T6 行动收件箱首页
首页只显示「要回复」和「要处理」，按紧急程度排序，每条显示摘要和快捷操作（起草回复、完成、稍后提醒）。「仅知悉」和「通知订阅」折叠成顶部的每日摘要卡片。

## T7 AI 起草回复
在线程上点「起草回复」，把线程内容发给 Claude 生成草稿，填入写信窗口供编辑。设置里可填写工作室的语气说明和签名。

## T8 搜索与稍后提醒
SQLite FTS5 全文搜索（发件人、主题、正文）。「稍后提醒」把邮件隐藏到指定时间再回到首页。
