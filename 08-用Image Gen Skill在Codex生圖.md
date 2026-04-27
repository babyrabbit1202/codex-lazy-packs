# Codex 懶人包 #08：用 Image Gen Skill 在 Codex 生圖

> 版本：v0.2（Codex Desktop 版）
> 更新日期：2026-04-27
> 適合對象：想在 Codex 裡直接生成教學圖片、封面、插圖、遊戲素材的新手

> 本篇重點：新手先用 Codex 內建 `Image Gen Skill`，不用設定 API Key；需要大量產圖、自動化或精準控制時，再改走 `OPENAI_API_KEY` 的 API / CLI 路線。

---

## 你會得到什麼？

完成後，你可以直接在 Codex 裡說：

```text
生成一張國中數學闖關遊戲的首頁背景圖，16:9，溫暖動漫風格，不要文字。
```

Codex 會使用內建 `imagegen` skill 產生圖片。

也可以說：

```text
以這張圖片為基礎，改成直式四格漫畫，含繁體中文對白。
```

Codex 會把它當成圖片編輯或再生成任務處理。

---

## 生圖有兩條路

| 路線 | 適合誰 | 會扣哪裡 | 是否需要 API Key |
|------|--------|----------|------------------|
| 路線 A：Codex 內建 Image Gen Skill | 新手、一般老師、少量圖片 | Codex / ChatGPT 內建工具額度 | 不需要 |
| 路線 B：OpenAI API / CLI | 進階使用者、大量產圖、自動化 | OpenAI API Platform 費用 | 需要 `OPENAI_API_KEY` |

> [!important]
> ChatGPT / Codex 訂閱與 OpenAI API Platform 是分開的帳務與用量系統。訂閱制能用 Codex，不代表 API 額度已開通；API 有自己的 billing、usage、rate limit。

---

## 路線 A：Codex 內建 Image Gen Skill（推薦新手）

### 什麼時候用？

只要你是一般生圖、修圖、做教材視覺，先用這條路。

適合：

- 教學投影片封面
- YouTube 縮圖草稿
- Word 試卷情境插圖
- 互動 HTML 背景圖
- 遊戲化教材角色、徽章、道具
- 簡單去背或透明背景素材

### 需要做什麼設定？

通常不用另外設定。只要 Codex Desktop 已經有 `imagegen` skill，就可以直接用自然語言請 Codex 生圖。

你可以請 Codex 檢查：

```text
幫我確認目前 Codex 是否有 imagegen skill。
```

若已安裝，Codex 應該能看到類似：

```text
C:\Users\<你>\.codex\skills\.system\imagegen\SKILL.md
```

### 圖片會存在哪裡？

Codex 內建生圖預設會存在：

```text
C:\Users\<你>\.codex\generated_images\
```

如果圖片要放進專案或 Obsidian，請直接跟 Codex 說：

```text
把剛剛生成的圖片複製到目前專案的 assets/images，檔名改成 math-game-cover.png。
```

或：

```text
把剛剛生成的圖片複製到 Obsidian 的 Codex 懶人包/附件 資料夾。
```

> [!tip]
> 專案真的要引用的圖片，不要只留在 `.codex\generated_images`。請讓 Codex 複製到專案資料夾或 Obsidian 附件資料夾，並回報完整路徑。

---

## 路線 A 的常用指令

### 產生新圖片

```text
生成一張 16:9 的國中數學研習簡報封面圖，主題是 AI 幫助老師備課，風格專業、明亮，不要放文字。
```

### 產生透明背景素材

```text
生成一個透明背景的數學闖關徽章，主題是「解方程式高手」，適合放在網頁遊戲裡。
```

> [!warning]
> 透明背景若是頭髮、煙霧、玻璃、半透明物件，可能比較難。Codex 可能會先用純色背景生成，再做去背；若需要真正原生透明背景，通常要走進階 API / CLI 路線。

### 修改既有圖片

```text
以這張圖片為基礎，改成 YouTube 縮圖風格，保留人物，背景改成明亮教室。
```

### 做漫畫或分鏡

```text
以這張圖片為出發點，畫一個直式四格漫畫，含繁體中文對白。
```

### 做簡報風格圖

```text
依照以下設計風格，生成一張 16:9 的簡報封面圖：
風格：Modern Anime Impressionism
色彩：#87CEEB、#F6CC41、#FFE2D1
限制：不要未來感 UI，不要生硬向量漸層。
```

---

## 路線 B：自己設定 OPENAI_API_KEY（進階）

### 什麼時候才需要？

以下情況再考慮 API / CLI：

- 要大量批次產圖
- 要固定輸出尺寸、品質、檔名、資料夾
- 要整合到自己的腳本或自動化流程
- 要明確選模型，例如 `gpt-image-2`
- 要追蹤 API 用量與成本
- 需要比內建工具更細的參數控制

### API 路線會怎麼計費？

API 路線使用 OpenAI API Platform，和 ChatGPT / Codex 訂閱分開。

你需要：

1. 到 OpenAI Platform 建立 API Key
2. 設定 billing / usage limit
3. 在本機設定 `OPENAI_API_KEY`
4. 用 API / CLI 呼叫圖片模型

API 用量可在這裡查看：

```text
https://platform.openai.com/usage
```

API key 建立位置：

```text
https://platform.openai.com/api-keys
```

> [!warning]
> 不要把 API Key 貼進對話、AGENTS.md、Obsidian 對外筆記、GitHub repo。請只設定在本機環境變數或不會被 git 追蹤的私有檔案。

---

## 新手不建議一開始做的事

| 不建議 | 原因 |
|--------|------|
| 一開始就設定 API Key | 容易混淆訂閱額度與 API 費用 |
| 把 API Key 寫進 AGENTS.md | 有外洩風險 |
| 直接批次大量產圖 | 可能快速消耗 API 額度 |
| 把所有生圖都指定 high quality | 成本會上升，草稿通常不需要 |
| 沒有指定用途就要求圖片很多細節 | 圖片容易失焦，後續難修改 |

---

## 建議給 Codex 的生圖提示格式

可以用這個模板：

```text
生成一張圖片：
用途：
尺寸比例：
主題：
畫面內容：
風格：
色彩：
文字：
限制：
輸出後請告訴我圖片存在哪裡。
```

範例：

```text
生成一張圖片：
用途：國中數學簡報封面
尺寸比例：16:9
主題：AI 幫老師備課
畫面內容：老師在溫暖教室裡整理教材，旁邊有筆電與數學符號
風格：現代動漫插畫，溫暖、明亮、有電影感
色彩：天空藍、陽光黃、暖桃色
文字：不要放文字
限制：不要高科技未來感 UI，不要浮誇光效，不要浮水印
輸出後請告訴我圖片存在哪裡。
```

---

## 踩坑筆記

| 狀況 | 原因 | 解法 |
|------|------|------|
| 不知道用掉哪個額度 | 內建 skill 與 API 是兩條路 | 內建生圖看 Codex / ChatGPT 工具額度；API 看 Platform usage |
| 以為訂閱制等於 API 免費 | ChatGPT / Codex 與 API Platform 分開計費 | API 需要另外設定 billing |
| 圖片找不到 | 預設存在 `.codex\generated_images` | 請 Codex 回報路徑，或複製到專案資料夾 |
| 中文文字不準 | 生圖模型對文字仍可能出錯 | 重要文字建議後製，或多次迭代修正 |
| 透明背景邊緣怪怪的 | 去背時顏色殘留或主體太複雜 | 換純色背景重產，或走進階 API / 後製流程 |
| API Key 外洩風險 | 把 key 寫進筆記或 repo | 只放本機環境變數，不提交到 GitHub |

---

## 官方資料來源

- OpenAI Image generation guide：<https://developers.openai.com/api/docs/guides/image-generation>
- OpenAI API Pricing：<https://openai.com/api/pricing/>
- ChatGPT 與 Platform 帳務分開：<https://help.openai.com/en/articles/9039756>
- Image generation rate limits：<https://help.openai.com/en/articles/6696591>

---

## 更新紀錄

| 日期 | 版本 | 更新內容 |
|------|------|----------|
| 2026-04-26 | v0.1 | 舊版：以 API Key + gpt-image-2 腳本為主 |
| 2026-04-27 | v0.2 | 改成 Codex Desktop Image Gen Skill 優先，API / CLI 改列進階路線，補上額度與計費分流說明 |
