# Codex 懶人包（OpenAI Codex 版）

> Claude Code 懶人包的 OpenAI Codex 平行版本。
> 把每份 MD 檔丟給 Codex，它會自動執行。
>
> ✅ **適用三種 Codex**：**Codex Desktop app（macOS/Windows）**、**Codex IDE 擴充（VSCode / Cursor / JetBrains）**、**Codex CLI**。三者**共用同一份** `~/.codex/config.toml` 與 `AGENTS.md`，所以設定做一次就好。

---

## 我用的是哪一種？

| 你的工具 | 怎麼裝 / 怎麼開 |
|---|---|
| **Codex Desktop app** | 從 [OpenAI 官網](https://developers.openai.com/codex/app)下載 macOS / Windows 安裝檔（**本懶人包預設場景**） |
| Codex IDE 擴充 | VSCode / Cursor / Windsurf → Marketplace 搜尋「ChatGPT」（OpenAI 官方）；JetBrains 系列也有對應 |
| Codex CLI | `npm install -g @openai/codex` |

**MCP 與 AGENTS.md 設定的三條路（任選）**：
1. **Desktop**：`設定 → Integrations & MCP` 介面新增 server；`設定 → Personalization` 編輯 AGENTS.md
2. **IDE**：齒輪 → MCP settings → Open config.toml
3. **CLI**：`codex mcp add <name> -- <command>`

> 設定動了一邊，三邊都會吃到。本懶人包以 **Desktop app GUI** 為主寫，每個 MCP 章節都附「手動編輯 `~/.codex/config.toml`」的 TOML 範例供 IDE / CLI 用戶或 Desktop 進階使用者參考。

---

## 為什麼有這份？

原本的懶人包是給 Claude Code 用的，但很多老師也在用 OpenAI Codex CLI。
這份是把同樣的工作流程，**改寫成 Codex 看得懂的版本**：

| 差異 | Claude Code | OpenAI Codex |
|---|---|---|
| 全域指令檔 | `~/.claude/CLAUDE.md` | `~/.codex/AGENTS.md` |
| 專案指令檔 | `./CLAUDE.md` | `./AGENTS.md` |
| 設定檔 | `~/.claude/settings.json`（JSON） | `~/.codex/config.toml`（TOML） |
| MCP 安裝指令 | `claude mcp add ...` | `codex mcp add ...` |
| Skill 機制 | 原生支援 | ❌ 沒有，用 prompt 模板或 shell 腳本取代 |
| Slash command | 原生支援 | ❌ 沒有 |
| 排程任務 | `mcp__scheduled-tasks__*` | ❌ 沒有，用系統 cron / Task Scheduler |

---

## 使用方式

1. 看影片了解原理（同 Claude 基本功系列）
2. 下載對應的懶人包（MD 檔）
3. 打開 OpenAI Codex CLI，把檔案內容貼進去
4. AI 自動執行，遇到需要手動操作的地方會暫停指示你

---

## 最低先備條件

- [ ] OpenAI 帳號（ChatGPT Plus / Pro / Business / Edu 訂閱，或 OpenAI API 額度）
- [ ] **Codex Desktop app** 已安裝且能登入（macOS / Windows）；或 IDE 擴充 / CLI 任一
- [ ] 電腦有網路連線

---

## 懶人包清單

| 編號 | 名稱 | 說明 |
|------|------|------|
| 00 | [環境建置](00-環境建置.md) | 安裝 Codex CLI、Git、GitHub CLI、uv |
| 01 | [連接 NotebookLM](01-連接-NotebookLM.md) | NotebookLM MCP（Codex 版） |
| 02 | [連接 GitHub](02-連接-GitHub.md) | gh CLI + GitHub Pages |
| 03 | [連接 Obsidian 第二大腦](03-建立第二大腦-Obsidian.md) | 先找 vault，再設定全域 AGENTS.md；跨專案讀寫可走資料夾授權或 MCPVault |
| 03+ | [第二大腦設定指南](04-第二大腦設定指南.md) | 三層結構 + AGENTS.md + 模板 |
| 04 | [連接 Supabase 資料庫](04-連接-Supabase-資料庫.md) | Supabase MCP（Codex 版） |
| 04.5 | [連接 Firebase 資料庫](04.5-連接-Firebase-資料庫.md) | Firebase MCP（Codex 版） |
| 05 | [安裝本地 AI Ollama](05-安裝本地AI-Ollama.md) | 本地模型，網頁工具用 |
| 06 | [設定 Gemini 免費 API](06-設定Gemini免費API.md) | Gemini 免費 API，網頁工具用 |
| 07 | [初始化班級工具工作模式](07-初始化班級工具工作模式.md) | AGENTS.md + 收工腳本 |
| 08 | [把 gpt-image-2 裝進 Codex](08-安裝gpt-image-2生圖.md) | CLI 腳本 + AGENTS.md 提示 |

---

## 不通用、需特別注意的章節

- **05 / 06**：跟 Codex / Claude 都無關，是「讓你做的網頁工具有 AI 能力」，兩邊通用。
- **07 收工同步**：Claude 版用 `/收工` Skill，Codex 沒有 Skill，改成 `~/codex-tools/shutdown.sh` + 在 AGENTS.md 寫觸發規則。
- **08 生圖**：Claude 版用 SKILL.md，Codex 版改成純 Python 腳本，由 AGENTS.md 規則觸發。
- **04 第二大腦設定指南**：Claude 版有「每週日自動知識重整排程」，Codex 沒原生排程，改成「系統 cron / Task Scheduler 觸發 codex 一行指令」。

---

## 授權

MIT License，與 Claude Code 懶人包相同。
