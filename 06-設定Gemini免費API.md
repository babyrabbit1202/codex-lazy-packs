# Codex 懶人包 #06：設定 Gemini 免費 API

> 版本：v0.1（Codex 版）
> 更新日期：2026-04-26

> 💡 **本懶人包跟 Claude Code 版幾乎一樣**——Gemini API 是給你做的「網頁工具」用的 AI 能力，跟用哪個 agent 開發無關。

---

## 這個懶人包會幫你做什麼？

設定 Google Gemini 免費 API，讓你用 Codex 做出來的工具也能有 AI 能力：
- 完全免費，不需要信用卡
- 不用裝任何軟體（只要 API Key）
- 比本地模型強（適合複雜推理、出題、分析）

---

## 先備條件

- [ ] Codex CLI 已安裝
- [ ] Google 帳號
- [ ] 電腦有網路

---

## 請 Codex 幫我執行以下步驟

### 步驟零：環境檢查

1. 作業系統 / 網路
2. 是否已有 `GEMINI_API_KEY` 環境變數（有就跳到步驟三驗證）

---

### 步驟一：申請 Gemini API Key

> 🖐️ 到 https://aistudio.google.com/apikey → Google 帳號登入 → Create API Key → 選或建專案 → 複製整串 Key。
>
> ⚠️ 不需要信用卡。Key 只顯示一次，記得複製。

把 Key 貼給 Codex。

---

### 步驟二：安全存放 API Key

寫到環境變數（不要寫進程式碼）：

**Windows**：
```bash
setx GEMINI_API_KEY "[使用者的Key]"
```

**macOS / Linux**：
```bash
echo 'export GEMINI_API_KEY="[使用者的Key]"' >> ~/.bashrc
source ~/.bashrc
```

> ⚠️ 不要寫在 HTML/JS 原始碼（公開網頁看得到），不要 push 到 GitHub（會被掃描盜用）。

---

### 步驟三：驗證

```bash
curl "https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash:generateContent?key=$GEMINI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"contents":[{"parts":[{"text":"請用繁體中文回答：1+1 等於多少？"}]}]}'
```

正常回 JSON → 設定成功。

---

### 步驟四：測試從網頁呼叫

建簡單 HTML 測試呼叫 Gemini API。

> ⚠️ 測試階段可在前端直呼。正式工具建議透過 Supabase Edge Functions / Cloud Functions 代理避免 Key 暴露。

✅ 「Gemini 免費 API 設定完成！」

---

## 完成！這樣用

對 Codex 說：「這個工具的 AI 功能用 Gemini 免費 API，從環境變數讀 Key。」

| 工具功能 | Gemini 做的事 |
|---|---|
| 智慧出題 | 根據單元和難度自動出題 |
| 作文批改回饋 | 分析寫作結構、給具體建議 |
| 教材差異化 | A/B/C 三種難度版本 |
| 學生回饋分析 | 整理全班回饋找共同問題 |
| 多語翻譯 | 140+ 語言 |

---

## 如果失敗

對 Codex 說：「Gemini API 懶人包失敗，幫我檢查。」

重新申請 Key：到 https://aistudio.google.com/apikey 重建。

---

## 常見問題

| 問題 | 解法 |
|---|---|
| API 回 401 | Key 無效，重申請 |
| API 回 429 | 超過免費速率，等一分鐘 |
| 環境變數讀不到 | Win 重啟終端機；mac/Linux `source ~/.bashrc` |
| 不確定 Key 對不對 | 到 aistudio.google.com/apikey 看 |
| 擔心被收費 | 免費方案不要信用卡，不會被扣 |

---

## 免費方案

| 項目 | 額度 |
|---|---|
| 費用 | $0 |
| 信用卡 | 不需 |
| 模型 | Gemini 2.5 Flash、Flash-Lite 等 |
| 速率 | 每分鐘有上限（教學使用不會超） |
| 期限 | 無期限 |

---

## 相關連結

- [Google AI Studio](https://aistudio.google.com)
- [Gemini API 文件](https://ai.google.dev/docs)
- [05 安裝本地 AI Ollama](05-安裝本地AI-Ollama.md)
