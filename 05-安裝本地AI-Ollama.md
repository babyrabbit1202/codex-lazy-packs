# Codex 懶人包 #05：安裝本地 AI（Ollama）

> 版本：v0.1（Codex 版）
> 更新日期：2026-04-26

> 💡 **本懶人包跟 Claude Code 版幾乎一樣**——Ollama 是讓你做出來的「網頁工具」有 AI 能力，跟你用 Codex 還是 Claude Code 來開發無關。

---

## 這個懶人包會幫你做什麼？

在電腦上裝免費本地 AI，讓你用 Codex 做出來的工具也能有 AI 能力：
- 完全免費，不需要 API Key 或信用卡
- 資料不離開電腦（隱私）
- 沒網路也能跑

---

## 先備條件

- [ ] Codex CLI 已安裝
- [ ] 記憶體 ≥ 8GB（建議 16GB+）
- [ ] 硬碟 ≥ 10GB

---

## 請 Codex 幫我執行以下步驟

### 步驟零：環境檢查

1. 作業系統
2. 記憶體大小：
   - Win：`wmic computersystem get totalphysicalmemory`
   - mac：`sysctl -n hw.memsize`
   - Linux：`free -h`
3. 硬碟可用空間 ≥ 10GB
4. 網路
5. 根據記憶體選模型：

| 記憶體 | 建議模型 | 大小 |
|---|---|---|
| < 8GB | ⚠️ 改用 Gemini 免費 API（懶人包 #06） | — |
| 8-16GB | `gemma4:e2b` | 約 5GB |
| ≥ 16GB | `gemma4:e4b` | 約 10GB |

---

### 步驟一：安裝 Ollama

**Windows**：
```bash
winget install --id Ollama.Ollama --accept-source-agreements --accept-package-agreements
```

**macOS**：到 https://ollama.com 下載安裝。

**Linux**：
```bash
curl -fsSL https://ollama.com/install.sh | sh
```

確認：`ollama --version`

---

### 步驟二：下載模型

```bash
ollama pull [步驟零決定的模型]
```

> 約 5-20 分鐘，看網速。

確認：`ollama list`

---

### 步驟三：測試本地 AI

```bash
ollama run [模型] "請用繁體中文回答：1+1 等於多少？"
```

---

### 步驟四：測試從網頁呼叫

建簡單 HTML：輸入框 + 送出 → 透過 `http://localhost:11434/api/generate` 呼叫 → 顯示回應。

> ✅ 「本地 AI 安裝完成！你做的網頁工具現在可以使用本地 AI 了。」

---

## 完成！這樣用

對 Codex 說：「這個工具的 AI 功能用本地 Ollama 的 Gemma 4 模型。」

| 工具功能 | 本地 AI 做的事 |
|---|---|
| 學習單自動生成 | 根據主題產題 |
| 作文簡易回饋 | 基本寫作建議 |
| 教材摘要 | 長文整理重點 |
| 翻譯輔助 | 英文教材翻中文 |

---

## 如果失敗

對 Codex 說：「Ollama 懶人包失敗，幫我檢查。」

完全重裝：
```bash
ollama rm [模型]
```
從步驟二重做。

---

## 常見問題

| 問題 | 解法 |
|---|---|
| `ollama: command not found` | 重啟終端機 |
| 下載中斷 | 重跑 `ollama pull`，會續傳 |
| 回應超慢（>30 秒） | 記憶體不足，改小模型或改 Gemini API |
| 瀏覽器呼叫 CORS 錯誤 | `OLLAMA_ORIGINS=* ollama serve` |

---

## 相關連結

- [Ollama 官網](https://ollama.com)
- [06 設定 Gemini 免費 API](06-設定Gemini免費API.md)
