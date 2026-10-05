# ste-zh：简化技术中文

一个 Agent skill。它让 Agent 按 ASD-STE100（简化技术英语）的写作原则，用中文向用户汇报结果。

生效后，Agent 的每一条回复：

- 第一句写结论。
- 用固定的状态词，例如「已完成」「未验证」「阻塞」。
- 写明每个结论是否验证，以及验证方法。
- 一个词只表示一个意思，一句只写一个事实。
- 情态词只用「必须」「不得」「可以」。
- 请用户决定时，列编号选项。

本 skill 不影响代码、代码注释、commit message 和 Agent 写入项目的文件。

## 文件

| 路径 | 内容 |
|---|---|
| `SKILL.md` | 入口：生效方式、适用范围、25 条规则、5 种回复模板、自检清单。 |
| `references/terminology.md` | ASD-STE100 关键词的中文译法、情态词、虚化动词替换表、状态词、模板用词、标点规则。 |
| `examples/result-report.md` | 结果汇报的改写前后对比。 |
| `examples/task-summary.md` | 多项总结的改写前后对比。 |

## 安装

把整个目录放到 Agent 的 skill 目录下。目录名必须是 `ste`。仓库名 `ste-zh` 与目录名不同，克隆时必须指定目录名。

Claude Code（全局）：

```bash
git clone https://github.com/dualface/ste-zh.git ~/.claude/skills/ste
```

其他支持 `SKILL.md` 格式的 Agent，按各自文档放到对应的 skill 目录。

## 使用

在对话中写以下任一句，开启本 skill：

- `/ste`
- 「用 STE 规范输出」
- 「按 ASD-STE100 汇报」

开启后，本会话的全部回复都按本 skill 写。写「停止 ste」或「stop ste」关闭。

本 skill 与其他输出风格冲突时，本 skill 优先。

## 限制

本 skill 靠 Agent 遵守上下文中的指令生效，没有程序强制执行。

- 会话很长时，Agent 可能逐渐退回默认写法。
- 上下文被压缩后，本 skill 的正文可能不在压缩结果中。之后的回复不再遵守本 skill。

出现以上情况时，重新输入 `/ste`。

需要每个会话都默认开启时，可以在 Agent 的全局规则中写「每个会话开始时加载 ste skill」。例：Claude Code 写入 `~/.claude/CLAUDE.md`，或 `~/.claude/rules/` 下的规则文件。

## 与 ASD-STE100 的关系

- ASD-STE100 是 ASD 发布的英文技术文档标准。它的规则只针对英文。
- 本 skill 把标准的原则改写为中文规则。规则的编号与条数是本 skill 自定的，与标准原文不对应。
- 本 skill 不包含标准原文，也不包含标准的词典。
- 本项目与 ASD 和 STE 维护组没有关联。ASD-STE100 与 Simplified Technical English 的权利属于 ASD。
- 标准原文可以从 [asd-ste100.org](https://www.asd-ste100.org/) 免费申请。

## 许可

MIT。全文见 [LICENSE](LICENSE)。
