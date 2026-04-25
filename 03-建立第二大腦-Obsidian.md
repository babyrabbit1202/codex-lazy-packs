# Codex 懶人包 #03：建立第二大腦（Obsidian）

> 版本：v0.1（Codex 版）
> 更新日期：2026-04-26

---

## 這個懶人包會幫你做什麼？

讓 Codex CLI 裝上「第二大腦」：讀寫 Obsidian vault、搜尋筆記、新增/編輯筆記，變成你的專屬助理。

---

## 先備條件

- [ ] Codex CLI 已安裝
- [ ] Google 帳號（用於 Google Drive 同步 vault）
- [ ] 電腦有網路連線
- [ ] Node.js 已安裝（沒裝步驟零會處理）

---

## 階段一：安裝 Obsidian 並建立 Vault

### 步驟零：環境檢查

1. 作業系統
2. 網路連線
3. **Node.js**：`node --version`
   - Windows 上 Codex bash 環境如果找不到 `node`，先試 `export PATH="/c/Program Files/nodejs:$PATH" && node --version`
   - 沒裝就裝（Win: `winget install --id OpenJS.NodeJS`；mac: `brew install node`）
4. `npx --version`
5. **Google Drive 桌面版**是否裝好（Win 看 `G:\`；mac 看 `~/Library/CloudStorage/GoogleDrive-*/`）
6. **Obsidian** 是否安裝

---

### 步驟一：安裝 Obsidian（如未安裝）

> 🖐️ 到 https://obsidian.md 下載安裝檔，安裝完先別開。

---

### 步驟二：安裝 Google Drive 桌面版（如未安裝）

> 🖐️ 到 https://www.google.com/drive/download/ 下載安裝。
> 安裝後登入 Google 帳號，系統匣（右下角）會出現雲朵圖示。
> 確認檔案總管裡看得到 Google Drive 磁碟機（Win 通常是 `G:\我的雲端硬碟\`）。

---

### 步驟三：在 Google Drive 建 vault 資料夾

> 🖐️ 詢問使用者要的 vault 名稱（建議：`secondbrain`）

建立：
```
Google Drive/
  └── [vault名稱]/
      ├── 教學素材/
      ├── 影片筆記/
      ├── 每日筆記/
      └── Templates/
```

記下 vault 完整路徑。

---

### 步驟四：用 Obsidian 開 vault

> 🖐️ 開 Obsidian → Open folder as vault → 選剛建的資料夾 → 顯示空 vault。

---

## 階段二：連接 MCP（讓 Codex 能讀寫筆記）

### 步驟五：安裝 mcpvault MCP Server

mcpvault 是主力 MCP，讓 Codex 能搜尋、讀取、編輯筆記。**不用 Obsidian 開著**也能跑，**不用裝任何 Obsidian 外掛**。

#### 5-1：全域安裝 mcpvault

```bash
# Windows（記得加 PATH）
export PATH="/c/Program Files/nodejs:$PATH" && npm install -g @bitbonsai/mcpvault

# macOS / Linux
npm install -g @bitbonsai/mcpvault
```

確認路徑：
- Windows：`where.exe mcpvault`（通常 `C:\Users\[你]\AppData\Roaming\npm\mcpvault.cmd`）
- macOS / Linux：`which mcpvault`

#### 5-2：註冊到 Codex

> ✅ 三種 Codex 共用 `~/.codex/config.toml`，做一次三邊都吃到。

**方法 A：Codex Desktop GUI（推薦）**

1. Desktop → 設定 → **Integrations & MCP** → **Add server**
2. 填：
   - Name：`obsidian`
   - Command（**Windows 必須完整路徑**）：`C:\Users\[你]\AppData\Roaming\npm\mcpvault.cmd`；macOS/Linux 直接寫 `mcpvault`
   - Args：vault 完整路徑
3. 儲存

**方法 B：手動編輯 `~/.codex/config.toml`**

> Desktop：設定 → Integrations & MCP → 「Open config.toml」可直接開檔。

Windows：

```toml
[mcp_servers.obsidian]
command = "C:\\Users\\[你]\\AppData\\Roaming\\npm\\mcpvault.cmd"
args = ["G:\\我的雲端硬碟\\[vault名稱]"]
```

macOS / Linux：

```toml
[mcp_servers.obsidian]
command = "mcpvault"
args = ["/Users/[你]/Library/CloudStorage/GoogleDrive-xxx/My Drive/[vault名稱]"]
```

> ⚠️ TOML 中 Windows 路徑必須用雙反斜線跳脫。
> ⚠️ Section 必須叫 `mcp_servers`（底線、複數），寫錯靜默忽略。

**方法 C：CLI**

```bash
codex mcp add obsidian -- mcpvault "G:/我的雲端硬碟/[vault名稱]"
```

---

### 步驟六：重啟 Codex 並驗證

> 🖐️ **Desktop**：完全結束 app 重開；**IDE**：Reload Window；**CLI**：`exit` 後重開。

驗證：
1. 對 Codex 說「列出 vault 根目錄的資料夾」
2. 能正確回傳結構 → 連接成功
3. Desktop 用戶在 Integrations & MCP 看 `obsidian` 顯示 ✅ 連線中

如果失敗：
1. Desktop：MCP 面板看 `obsidian` 狀態；CLI：`codex mcp list`
2. 終端機手動測 MCP server：
   ```bash
   echo '{"jsonrpc":"2.0","id":1,"method":"tools/list"}' | mcpvault "[vault路徑]"
   ```
   能回 JSON 工具清單 → mcpvault 本身沒問題
3. 確認 `~/.codex/config.toml` 路徑正確、section 名 `mcp_servers`（底線複數）

---

### 步驟七：建立 AGENTS.md（Codex 的「班規」）

> ✅ Codex 三種版本都會讀取當前工作目錄（含上層）的 `AGENTS.md`。
> Desktop 用戶也可以同時在 **設定 → Personalization → Custom instructions** 寫共用偏好（會寫進 `~/.codex/AGENTS.md`，全域生效）。

在 vault 根目錄建 `AGENTS.md`：

> 🖐️ 詢問使用者：教什麼科目、什麼年級、語言偏好

範例：

```markdown
# 我的 Obsidian — AGENTS.md

## 關於我
- 我是[科目][年級]老師
- 這個 vault 是我的教學第二大腦

## 語言偏好
- 所有回應請使用繁體中文

## 工作規則
- 新增筆記時自動加 frontmatter（title、date、tags）
- 搜尋筆記時優先搜尋教學素材相關內容
```

---

### 步驟八：建立第一篇正式筆記

幫使用者建一篇有意義的筆記，例如「本學期教學計畫」或「我的教學工具清單」。

---

## 完成！這樣用

| 你說的話 | Codex + Obsidian 會做的事 |
|----------|----------------------------|
| 「搜尋我的筆記有沒有跟 XXX 相關的」 | BM25 搜尋整個 vault |
| 「新增一篇筆記紀錄今天的教學反思」 | 建新筆記含 frontmatter |
| 「整理這篇筆記的重點」 | 讀內容並摘要 |
| 「從 XXX 筆記延伸教學點子」 | 讀指定筆記、給延伸建議 |

---

## 如果失敗

對 Codex 說：「Obsidian 懶人包失敗，幫我檢查重新處理。」

完全重置：
- **Desktop**：設定 → Integrations & MCP → 找 `obsidian` → 刪除
- **手動**：編輯 `~/.codex/config.toml` 移除 `[mcp_servers.obsidian]` 段
- **CLI**：`codex mcp remove obsidian`

從步驟五重做。

---

## 常見問題

| 問題 | 解法 |
|------|------|
| mcpvault 搜不到筆記 | 確認 vault 路徑正確；TOML 中 Windows 路徑用 `\\` 雙反斜線 |
| Google Drive 同步衝突 | 兩台裝置不要同時編同篇筆記 |
| `npx: command not found` | 確認 Node.js 已裝，重啟終端機 |
| Windows bash 找不到 node | `export PATH="/c/Program Files/nodejs:$PATH"` |
| 重啟後 MCP 工具不見 | 確認 `command` 用**完整路徑**（如 `C:\\Users\\...\\mcpvault.cmd`） |
| TOML 寫了沒生效 | 確認檔在 `~/.codex/config.toml`、語法正確；用 `codex mcp list` 檢查 |
| Obsidian 要裝外掛嗎？ | **不用**。mcpvault 直接讀寫資料夾，不經 Obsidian app |

---

## 同步方案

| 方案 | 差異 |
|------|------|
| Google Drive（預設） | vault 建在 Google Drive 資料夾內 |
| Obsidian Sync（$4/月） | vault 建在任意位置，Obsidian 內設定同步 |
| iCloud（macOS / iOS） | vault 建在 iCloud Drive 資料夾內 |

差別只在 vault 位置，MCP 連接方式完全相同。

---

## 相關連結

- [mcpvault GitHub](https://github.com/bitbonsai/mcpvault)
- [Obsidian 官網](https://obsidian.md)
- [Codex MCP 官方文件](https://developers.openai.com/codex/mcp)
