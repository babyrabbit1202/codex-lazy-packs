# Codex 懶人包 #01：連接 Google NotebookLM

> 版本：v0.1（Codex 版）
> 更新日期：2026-04-26

> 📌 **本懶人包可獨立執行**：會自動檢查並安裝所需工具。

---

## 這個懶人包會幫你做什麼？

讓 Codex CLI 能直接操控 NotebookLM：建 notebook、上傳資料來源、產生簡報/資訊圖表/音訊/影片/心智圖/測驗等，成品自動下載到本機資料夾。

---

## 原理

```
Codex CLI ←(MCP 協定)→ nlm（翻譯官）←(Google 登入)→ NotebookLM
```

`nlm` 是 stdio MCP server，Codex 透過 MCP 協定呼叫它，它再去模擬瀏覽器操作 NotebookLM。

> 💡 **跟 Claude Code 版的差別**：MCP server 本體（`notebooklm-mcp-cli`）一模一樣，只是註冊到 Codex 的方式不同（Codex 用 `~/.codex/config.toml`，Claude Code 用 `~/.claude/settings.json`）。

---

## 先備條件

- [ ] Codex CLI 已安裝
- [ ] Google 帳號
- [ ] 電腦有網路連線

---

## 請 Codex 幫我執行以下步驟

### 步驟零：環境檢查

1. 作業系統
2. `git --version`，沒有就裝
3. `uv --version`，沒有則步驟一安裝
4. 網路連線
5. `codex --version`

---

### 步驟一：安裝 uv（如果沒裝）

**Windows（PowerShell）**：
```bash
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

**macOS / Linux**：
```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

---

### 步驟二：安裝 NotebookLM MCP CLI

```bash
uv tool install notebooklm-mcp-cli
```

確認：
```bash
nlm --version
```

> `nlm: command not found` → 重開終端機；仍失敗代表 uv 工具路徑沒進 PATH，告訴使用者把 `uv tool dir --bin` 印出的路徑加進 PATH。

---

### 步驟三：登入 Google 帳號

```bash
nlm login
```

> 🖐️ 瀏覽器會開 Google 登入頁，登入後 CLI 自動擷取認證。

確認：
```bash
nlm doctor
```

---

### 步驟四：把 NotebookLM 註冊為 Codex 的 MCP server

**方法 A：用 codex CLI（推薦）**

```bash
codex mcp add notebooklm -- nlm mcp
```

> 註：`nlm setup add codex-cli` 在某些版本可能尚未支援，**用 `codex mcp add` 最保險**。

**方法 B：手動編輯 `~/.codex/config.toml`**

在 `~/.codex/config.toml` 加入：

```toml
[mcp_servers.notebooklm]
command = "nlm"
args = ["mcp"]
```

> 💡 路徑：Windows 是 `C:\Users\<你>\.codex\config.toml`，macOS/Linux 是 `~/.codex/config.toml`。檔案不存在就新建。

---

### 步驟五：建立本地資料夾

在 Documents 下建：
```
Documents/
  └── NotebookLM/
      ├── slides/
      ├── infographics/
      ├── audio/
      ├── video/
      ├── docs/
      ├── sheets/
      ├── mindmaps/
      └── quizzes/
```

---

### 步驟六：重啟 Codex 並驗證

> 🖐️ 完全關閉 Codex CLI 再重啟（`exit` 後重開）。

驗證：
1. 對 Codex 說「列出我的 NotebookLM 筆記本清單」
2. 能成功列出（即使空的）→ 連接成功

---

### 步驟七：功能測試

1. 建一個叫「測試筆記本」的 notebook
2. 確認建立成功
3. 刪除它
4. ✅ 「全部完成！Codex 已連接 NotebookLM。」

---

## 如果失敗

對 Codex 說：「NotebookLM 懶人包執行失敗，清除設定重跑。」

復原：
```bash
codex mcp remove notebooklm
uv tool uninstall notebooklm-mcp-cli
nlm logout 2>/dev/null
```

或手動編輯 `~/.codex/config.toml` 移除 `[mcp_servers.notebooklm]` 段。

---

## 常見問題

| 問題 | 解法 |
|------|------|
| `nlm: command not found` | 重開終端機；把 `uv tool dir --bin` 路徑加進 PATH |
| 登入後 `nlm doctor` 顯示未認證 | 重跑 `nlm login` |
| Codex 看不到 NotebookLM 工具 | 確認 `codex mcp list` 有 `notebooklm`，沒有就重做步驟四 |
| `codex mcp` 指令找不到 | 確認 Codex CLI 版本夠新（`npm i -g @openai/codex@latest`） |
| Windows 上指令格式錯誤 | 用 PowerShell 或 Git Bash，別用 CMD |

---

## 相關連結

- [notebooklm-mcp-cli GitHub](https://github.com/jacob-bd/notebooklm-mcp-cli)
- [Codex MCP 官方文件](https://developers.openai.com/codex/mcp)
