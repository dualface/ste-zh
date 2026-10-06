# ste-zh：簡化技術中文

一個 Agent skill。它讓 Agent 按 ASD-STE100（簡化技術英語）的寫作原則，用中文向使用者回報結果。

生效後，Agent 的每一條回覆：

- 第一句寫結論。
- 用固定的狀態詞，例如「已完成」「未驗證」「阻塞」。
- 寫明每個結論是否驗證，以及驗證方法。
- 一個詞只表示一個意思，一句只寫一個事實。
- 情態詞只用「必須」「不得」「可以」。
- 請使用者決定時，列編號選項。

本 skill 不影響程式碼、程式碼註解、commit message 和 Agent 寫入專案的檔案。

## 檔案

| 路徑 | 內容 |
|---|---|
| `SKILL.md` | 入口：生效方式、適用範圍、25 條規則、5 種回覆模板、自檢清單。 |
| `references/terminology.md` | ASD-STE100 關鍵詞的中文譯法、情態詞、虛化動詞替換表、狀態詞、模板用詞、標點規則。 |
| `examples/result-report.md` | 結果回報的改寫前後對比。 |
| `examples/task-summary.md` | 多項總結的改寫前後對比。 |

## 安裝

把整個目錄放到 Agent 的 skill 目錄下。目錄名必須是 `ste`。儲存庫名稱 `ste-zh` 與目錄名不同，複製時必須指定目錄名。

Claude Code（全域）：

```bash
git clone https://github.com/dualface/ste-zh.git ~/.claude/skills/ste
```

其他支援 `SKILL.md` 格式的 Agent，依各自的說明文件放到對應的 skill 目錄。

## 使用

在對話中寫以下任一句，開啟本 skill：

- `/ste`
- 「用 STE 規範輸出」
- 「按 ASD-STE100 回報」

開啟後，本工作階段的全部回覆都按本 skill 寫。寫「停止 ste」或「stop ste」關閉。

本 skill 與其他輸出風格衝突時，本 skill 優先。

## 限制

本 skill 靠 Agent 遵守上下文中的指令生效，沒有程式強制執行。

- 工作階段很長時，Agent 可能逐漸退回預設寫法。
- 上下文被壓縮後，本 skill 的正文可能不在壓縮結果中。之後的回覆不再遵守本 skill。

出現以上情況時，重新輸入 `/ste`。

需要每個工作階段都預設開啟時，可以在 Agent 的全域規則中寫「每個工作階段開始時載入 ste skill」。例：Claude Code 寫入 `~/.claude/CLAUDE.md`，或 `~/.claude/rules/` 下的規則檔案。

## 與 ASD-STE100 的關係

- ASD-STE100 是 ASD 發布的英文技術文件標準。它的規則只針對英文。
- 本 skill 把標準的原則改寫為中文規則。規則的編號與條數是本 skill 自定的，與標準原文不對應。
- 本 skill 不包含標準原文，也不包含標準的詞典。
- 本專案與 ASD 和 STE 維護組沒有關聯。ASD-STE100 與 Simplified Technical English 的權利屬於 ASD。
- 標準原文可以從 [asd-ste100.org](https://www.asd-ste100.org/) 免費申請。

## 授權

MIT。全文見 [LICENSE](LICENSE)。
