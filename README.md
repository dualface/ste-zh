# ste-zh：简化技术中文规范

**语言 / Language：** 简体中文 | [繁體中文](https://github.com/dualface/ste-zht)

一个面向 AI Agent 的任务汇报规范 skill。借鉴航空与工业界严谨的 **ASD-STE100**（Simplified Technical English，简化技术英语）原则，约束 Agent 用清晰、严密且无歧义的中文汇报工作结果。

Agent 的日常汇报常常过程冗长、推测含糊，或者漏掉关键的验证证据。开启 `ste-zh` 后，Agent 将遵循受控中文规范进行汇报：**结论先行、实证说话、状态固定、一目了然**。

> 💡 本 skill 仅规范 Agent 向用户汇报与讨论时的表达方式，绝不影响项目代码、注释、Commit 信息及生成的文件内容。

---

## 为什么需要 ste-zh？

同一个 Bug 修复任务，常见汇报与规范化汇报的差异如下：

**常见 AI 汇报（冗长、推测、不报实证）：**

> 「好的，我先看了一下登录相关的代码，发现问题应该是出在 token 刷新的逻辑上，大概是并发的时候两个请求同时去刷新导致的。然后我对 `auth/refresh.ts` 进行了修改，加了一个锁来实现串行化处理，另外顺手把一些日志之类的也优化了一下。测试基本都跑过了，应该没什么问题，不过 iOS 那边因为没有环境暂时没法验证。你看要不要我再把重试逻辑也一起改了？」

**`ste-zh` 规范化汇报（结论先行、实证可查、便于决策）：**

> **已完成**：修复登录后偶发退出的问题。`npm test auth` 已验证，iOS 端未验证。
>
> **原因**：两个请求同时刷新 token，后完成的刷新导致先获取的 token 失效。  
> **证据**：`auth/refresh.ts:42` — 刷新前未检查是否已有进行中的刷新。
>
> **改动**：
>
> - `auth/refresh.ts`：同一时间仅允许一个刷新请求，其他请求等待该结果。
> - `auth/logger.ts`：删除 3 处重复日志（超出任务范围）。
>
> **验证**：
>
> - 已验证：`npm test auth`（48 项测试全部通过）。
> - 未验证：iOS 端（本机缺少构建环境）。
>
> **待确认**：  
> 是否修改重试逻辑？
>
> 1. 保持现状（推荐）
> 2. 本次一同修改

---

## 核心特性

- **结论先行**：回复首句直奔主题，多任务并行时数秒内即可扫视完毕。
- **状态固定**：仅使用「已完成」「部分完成」「未验证」「阻塞」等明确状态词，杜绝「应该搞定了」这类模棱两可的说辞。
- **必须标明验证**：每项改动都写明是否验证、怎样验证，杜绝虚假汇报。
- **一词一义与主动语态**：规范专用词汇，消除虚化动词与歧义长句，降低阅读心智负担。
- **选项化决策**：遇到需要人工定夺的决策点，自动整理为带推荐倾向的编号选项，用户输入数字即可快速推进。

---

## 安装

### 推荐：使用 `skills` CLI 安装

[`skills`](https://github.com/vercel-labs/skills) 是 Vercel Labs 推出的 Agent Skill 管理工具，支持 Claude Code、Cursor、Codex 等主流环境。工具会自动按配置将本技能安装至 `ste` 目录。

全局安装（所有项目通用）：

```bash
npx skills add dualface/ste-zh -g
```

安装到当前项目：

```bash
npx skills add dualface/ste-zh
```

仅为指定 Agent 安装（例如 Claude Code）：

```bash
npx skills add dualface/ste-zh -g -a claude-code
```

更新与卸载：

```bash
npx skills update ste -g
npx skills remove --global ste
```

### 手动安装

将本仓库克隆至 Agent 的 skills 目录下即可。由于本 skill 的标准名称为 `ste`，克隆时请确保目标目录命名为 `ste`：

**Claude Code（全局）：**

```bash
git clone https://github.com/dualface/ste-zh.git ~/.claude/skills/ste
```

其他支持 `SKILL.md` 规范的 Agent，参考各自文档将文件放置在对应的 skills 路径下。

---

## 使用方式

在对话中输入以下任意指令，即可启用本 skill：

- `/ste`
- 「用 STE 规范输出」
- 「按 ASD-STE100 汇报」

启用后，当前会话中的所有汇报都将严格按照本规范组织。如需退出，发送「停止 ste」或「stop ste」即可。

> 当本 skill 与其他输出风格存在冲突时，优先遵循本 skill。

### 注意事项

- **运行机制**：本 skill 靠 Agent 遵守上下文中的指令生效，没有程序强制执行。
- **上下文衰减**：在极长会话或发生上下文压缩（compaction）后，Agent 可能会遗忘指令规范。若发现输出风格退化，随时重新发送 `/ste` 即可恢复。
- **全局常驻**：如希望每个会话默认开启，可将「每个会话开始时加载 ste skill」写入 Agent 的全局规则中（例如 Claude Code 的 `~/.claude/CLAUDE.md` 或 `~/.claude/rules/` 规则文件）。

---

## 实战配合：与 Kander 协同

作者在使用 [Kander](https://github.com/dualface/kander)（多 Agent 看板调度工具）并行调度多个任务时，重度依赖本 skill：

- **高效扫视**：看板上多张任务卡并行流转，每张卡的汇报第一句就是核心结论，几秒钟即可过完所有任务进展。
- **实证门禁**：Kander 严格要求任务成果可核验，配合本 skill 强制区分「已验证」与「未验证」，未验证的结论无法混进「已完成」。
- **极简决策**：需要人工确认的阻塞点被清晰格式化为编号选项，在 Agent 会话中回复数字即可快速放行。

---

## 设计背景与 ASD-STE100 的关系

- **关于标准**：[ASD-STE100](https://www.asd-ste100.org/)（Simplified Technical English）由欧洲航空航天与国防工业协会（ASD）维护，原本是针对英文技术维护文档制定的受控语言规范，用于消除歧义与理解偏差。
- **中文改写**：本项目提炼了 ASD-STE100 的核心原则，并结合 AI 交互特点重构为一套中文实用规则体系。条目编号与内容均为独立设计，并非原文直译。
- **独立声明**：本项目为个人开源项目，未收录 ASD-STE100 原文与受控词典，与 ASD 组织无隶属关系。ASD-STE100 与 Simplified Technical English 的权利属于 ASD。如需研读 ASD-STE100 英文原版标准，可在其官网免费申请。

---

## 核心文件

| 路径                        | 说明                                                                              |
| --------------------------- | --------------------------------------------------------------------------------- |
| `SKILL.md`                  | skill 入口：生效机制、适用范围、25 条核心规则、5 种标准化输出模板及自检清单       |
| `references/terminology.md` | 术语字典：ASD-STE100 关键词译法、受控情态词、虚化动词替换表、固定状态词与标点规范 |
| `examples/result-report.md` | 单任务结果汇报改写示例（改写前后对比）                                            |
| `examples/task-summary.md`  | 多任务与迭代需求总结改写示例（改写前后对比）                                      |

---

## 许可证

基于 MIT 协议开源。详见 [LICENSE](LICENSE)。

---

## 作者的其他项目

欢迎体验作者 [dualface](https://github.com/dualface) 的其他项目：

- [Kander](https://github.com/dualface/kander)：规则驱动的多 Agent 看板调度工具，内置独立审核与自动化交付门禁。
- [Ullage](https://github.com/dualface/ullage-cli)：本地守护进程与 CLI 工具，查看 Claude、ChatGPT、Grok、Cursor 等订阅的用量。
- [QuickTUI](https://quicktui.ai/)：适用于各类编码 Agent 的手机端完整终端，支持自托管直连，单台主机免费。
