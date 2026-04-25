# Codex 懶人包 #04：連接 Supabase 資料庫

> 版本：v0.1（Codex 版）
> 更新日期：2026-04-26

---

## 這個懶人包會幫你做什麼？

讓 Codex CLI 直接操控 Supabase 雲端資料庫：
- 自然語言建表、新增/查詢/修改/刪除資料（不用學 SQL）
- 讓你做的網頁工具能「記住」資料
- 多人同時用同一份資料

---

## 先備條件

- [ ] Codex CLI 已安裝
- [ ] GitHub 帳號（Supabase 用 GitHub 登入）
- [ ] 電腦有網路連線

---

## 階段一：建立 Supabase 帳號與專案

### 步驟零：環境檢查

1. 作業系統 / 網路 / `node --version`（沒裝就裝） / `npx --version`
2. `gh auth status`（用來登入 Supabase）

---

### 步驟一：註冊 Supabase 帳號

> 🖐️ 到 https://supabase.com → Start your project → Continue with GitHub → 授權。

---

### 步驟二：建立 Supabase 專案

> 🖐️ Dashboard → New Project：
> - Project name：`my-teaching-tools`
> - Database Password：設一個（記住）
> - Region：`Northeast Asia (Tokyo)` 之類離你近的
> - Create new project，等 1-2 分鐘

---

### 步驟三：取得專案憑證

> 🖐️ Project Settings（齒輪）→ API → 複製：
> - **Project URL**（`https://xxxxxxxx.supabase.co`）
> - **service_role key**（點 Reveal 複製）
>
> ⚠️ service_role key 有完整權限，不要分享、不要進公開 repo。

把這兩個值給 Codex。

---

## 階段二：連接 MCP

### 步驟四：把 Supabase 註冊為 Codex 的 MCP server

> ✅ 三種 Codex 共用 `~/.codex/config.toml`。

**方法 A：Codex Desktop GUI（推薦）**

1. Desktop → 設定 → **Integrations & MCP** → **Add server**
2. 填：
   - Name：`supabase`
   - Command：`npx`
   - Args（每行一個或用空白分隔，依介面）：`-y`、`@supabase/mcp-server-supabase@latest`、`--supabase-url`、`<Project URL>`、`--supabase-service-role-key`、`<service_role_key>`
3. 儲存

**方法 B：手動編輯 `~/.codex/config.toml`**

```toml
[mcp_servers.supabase]
command = "npx"
args = [
  "-y",
  "@supabase/mcp-server-supabase@latest",
  "--supabase-url", "https://xxxxxxxx.supabase.co",
  "--supabase-service-role-key", "<service_role_key>"
]
```

> 💡 想避免把 key 寫死在 config，可以改用環境變數：
> ```toml
> [mcp_servers.supabase]
> command = "npx"
> args = ["-y", "@supabase/mcp-server-supabase@latest", "--supabase-url", "https://xxxxxxxx.supabase.co"]
>
> [mcp_servers.supabase.env]
> SUPABASE_SERVICE_ROLE_KEY = "<key>"
> ```
> 並在 args 裡用 `--supabase-service-role-key` 配對應的環境變數讀取（依 server 版本而定）。

**方法 C：CLI**

```bash
codex mcp add supabase -- npx -y @supabase/mcp-server-supabase@latest --supabase-url <Project URL> --supabase-service-role-key <service_role_key>
```

---

### 步驟五：重啟 Codex 並驗證

> 🖐️ **Desktop**：完全結束 app 重開；**IDE**：Reload Window；**CLI**：`exit` 後重開。

對 Codex 說「列出 Supabase 中有哪些資料表」（新專案應為空，能成功查詢就代表連接成功）。

---

### 步驟六：功能測試

1. 建測試表 `test_table`（id 自動遞增、name 文字、created_at 時間戳）
2. 新增一筆 name = 「Supabase 連接測試成功」
3. 查資料、確認讀得到
4. 刪除測試表
5. ✅ 「連接測試成功！」

---

### 步驟七：設定自動防暫停（重要）

Supabase 免費專案閒置一週會自動暫停。

**Codex 沒有原生排程**，請用系統排程器：

> ⚠️ **Codex Desktop / IDE 沒有 CLI 排程指令**。最簡單可行的做法：

**方法 1：每週手動對 Codex 說一次「查一下 Supabase」**（最不容易出錯）

**方法 2：直接打 Supabase 防暫停**（與 Codex 無關）—— 用系統排程跑一個 curl 查一下 anon key 就行：
- Windows 工作排程器或 macOS/Linux cron 排每週一次：
  ```bash
  curl -s "https://<Project URL>/rest/v1/<任意table>?select=*&limit=1" \
    -H "apikey: <anon key>" -H "Authorization: Bearer <anon key>" >/dev/null
  ```

**方法 3：CLI 用戶**（如果有裝）：
```
0 9 * * 1 codex exec "查 Supabase 任意一個資料表的資料筆數" >> ~/.codex/supabase-keepalive.log 2>&1
```

---

## 完成！這樣用

| 你說的話 | Codex + Supabase 會做的事 |
|----------|----------------------------|
| 「建一個學生成績的資料表」 | 建表 + 設欄位 |
| 「新增一筆學生成績」 | INSERT |
| 「查全班數學成績平均」 | SQL 查詢 + 計算 |
| 「做一個成績管理網頁，連接資料庫」 | 產前端 + 連 Supabase + 持久化 |

---

## 如果失敗

對 Codex 說：「Supabase 懶人包失敗，幫我檢查重新處理。」

完全重置：
- **Desktop**：設定 → Integrations & MCP → 刪 `supabase`
- **手動**：編輯 `~/.codex/config.toml` 刪 `[mcp_servers.supabase]` 段
- **CLI**：`codex mcp remove supabase`

從步驟四重做。

---

## 常見問題

| 問題 | 解法 |
|------|------|
| `npx: command not found` | 確認 Node.js 已裝，重啟終端機 |
| 連接後查詢失敗 | 確認用的是 service_role key（不是 anon key） |
| Supabase 顯示「Paused」 | Dashboard 點 Restore；之後設好排程防暫停 |
| 不確定哪個 key | API 設定頁有兩個：用 **service_role**，不是 **anon** |

---

## 免費方案

| 項目 | 額度 |
|------|------|
| 資料庫 | 500 MB |
| 檔案 | 1 GB |
| 月活用戶 | 50,000 |
| API 請求 | 無限 |
| 專案數量 | 2 個 |

> ⚠️ 免費專案閒置一週暫停，重啟即可。

---

## 安全

- service_role key 不要進公開 repo
- 示範用假資料，不要放真實學生個資
- Supabase MCP 適合開發/測試，正式使用注意隱私法規

---

## 相關連結

- [Supabase 官網](https://supabase.com)
- [Supabase MCP 官方文件](https://supabase.com/docs/guides/getting-started/mcp)
- [Codex MCP 官方文件](https://developers.openai.com/codex/mcp)
