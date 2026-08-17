# orpheus-aviary · 奥尔菲斯百鸟苑

> 一个 AI 助手 + 一群可独立使用、也能被 AI 调用的工具。本地优先、开源、全线 TypeScript。

orpheus-aviary 把「给人用的好工具」和「给 agent 用的好接口」做成同一件东西：每个工具都有适合人类的图形界面，
也都能被 AI 助手通过 CLI / `skill.md` 调用。数据本地优先，按需接入自部署同步——你的笔记、音乐、配置始终在自己手里。

## 当前情况

| 工具 | 是什么 | 状态 |
|---|---|---|
| 🦉 **[owl](https://github.com/orpheus-aviary/owl)** | AI 原生 Markdown 笔记 | 🚀 **0.6.1 已发**（macOS arm64 + 网页版） |
| 🌉 **[skybridge](https://github.com/orpheus-aviary/skybridge)** | local-first 多设备 / 多账号同步桥 | 📦 **npm 0.1.4**（三包公开发布） |
| 🎵 **[lark](https://github.com/orpheus-aviary/lark)** | 音乐播放 / 下载 / 歌词工具 | 🚀 **0.3.0 已发**（macOS arm64，多设备同步） |
| 🐦 **jay** | 终端 AI 助手（统一编排各工具） | ⏳ 规划中（TS 重写） |

技术栈：Electron · Fastify · React + shadcn/ui · better-sqlite3 + drizzle · CodeMirror · pnpm monorepo。
统一数据目录 `~/orpheus-aviary-nest/`——只需二进制 + 这个目录即可启动与迁移。

---

## 🦉 owl —— AI 原生的 Markdown 笔记

开源的桌面笔记软件：适合人类的 GUI + 内置 Agent 助手，同时能被外部 agent 通过 CLI 调用。

**突出优势**
- **AI 原生、双向可用**：内置 Agent 助手；也能作为工具被外部 agent 调用（`owl` CLI + 一键导出 `skill.md`）。
- **本地优先、数据自主**：默认全本地、可离线用；需要时接入**自部署**同步服务，数据不经第三方托管。
- **多账号 per-profile 隔离 + 免密快切**：每个账号独立工作区，本地库永不被账号同步污染；本地 ↔ 云端工作区一键免密切换。
- **多设备实时同步**：基于 skybridge 的 operation-log 同步 + SSE 实时推送 + 冲突可见。
- **全栈 TypeScript、开源**：GUI / daemon / CLI 同一套 core，行为一致。

> 下载（macOS arm64）：见 [Releases](https://github.com/orpheus-aviary/owl/releases) · CLI：`npm i -g @orpheus-aviary/owl-cli`

## 🌉 skybridge —— local-first 同步桥

为 owl、lark 等工具提供多设备 / 多账号同步的远程桥，三包发布在 npm
（`@orpheus-aviary/skybridge-{server,client,proto}`）。

**突出优势**
- **operation-log 同步，而非文件级覆盖**：每个写操作记 append-only change log，push / pull + LWW 归一化，冲突可控。
- **自部署、单命令、轻量**：一条命令起一个 Node 服务，承接 auth、device 注册、workspace、change 推拉、SSE 通知。
- **为多设备 / 多账号而生**：server_id 作工作区锚点、权威 server 时间做 LWW 归一化、refresh-token 轮换、设备吊销。
- **可复用**：同一套 client / proto 给 owl、lark 复用，新工具接入成本低。

## 🎵 lark —— 会自己找歌、自己配词的音乐播放器

桌面音乐工具：粘一条链接或直接输歌名就能下载，歌词自动找、自动对齐，曲库和歌单都在本地。

**突出优势**
- **下载即入库**：bilibili 链接、收藏夹、合集，或者干脆只输一个歌名——LLM 负责把「歌名」变成「哪个视频」，
  下载、配歌词、写进曲库一条龙；下载来的 AAC **原样重封装不再转码**，也可以让模型顺手把「【官方MV】…」
  这样的标题读成歌名与歌手（0.3 起）。
- **歌曲链接体系**：每首歌记住自己的来源，文件丢了、换了设备，凭链接自动重下。
- **统一缓存模型**：可以设上限、按最久未访问自动清理，但**只清理确定能重下的**——你自己导入的文件是资产，永不自动删。
- **AI 可用**：`lark` CLI 全命令覆盖，`--json` 下「exit 0 ⇔ 一条成功信封」，另可一键导出 `skill.md` 给 agent。
- **自带 ffmpeg**：安装包内含一份自建的 LGPL FFmpeg（零外部库），下载与导入开箱即用，不必先去配环境。
- **导入按文件是什么收**（0.3 起）：m4a/mp4 · aac · mp3 · flac · wav · ogg/oga/opus 都收，不看扩展名看内容；
  曲库只保留一种格式（m4a），导入即转换，无损源会告诉你副本损失了什么。
- **多设备同步**（0.2 起）：经 skybridge 同步曲库、歌单与歌词，**歌曲文件本身不同步**——各设备凭来源信息按需下载。冲突不猜不合并，摆出两边的值让你选。

> 下载（macOS arm64）：见 [Releases](https://github.com/orpheus-aviary/lark/releases) · CLI：`npm i -g @orpheus-aviary/lark-cli`

---

## 下一步开发计划

- **📱 owl 移动端** —— 在桌面版之上扩展到移动设备，复用同一套 core 与 skybridge 同步。
- **🐦 jay 终端 AI 助手** —— TS 化，统一编排百鸟苑各工具。

更远期：skybridge 多设备同步 GA → owl 1.0.0。

---

<sub>开源 · 本地优先 · AI 原生 — orpheus-aviary</sub>
