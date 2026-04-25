# Codex 懶人包 #08：把 gpt-image-2 裝進 Codex

> 對應影片：Claude 基本功 EP11（Codex 平移版）
> 目標：五分鐘內完成安裝，讓 Codex 能用一句話生圖
> 費用：第一次需儲值 OpenAI 最少 US$5；每張圖約 **NT$0.3**（low 品質）

---

## 你會得到什麼

裝好後，在**任何**專案資料夾裡對 Codex 說：

> 「畫一隻穿西裝的龍蝦」

Codex 會自動跑 `~/codex-tools/draw.py` 用 **gpt-image-2** 生圖，存到當前資料夾的 `slides/generated/`（或 `./generated/`）。

> 💡 **跟 Claude Code 版的差別**：Claude Code 版用 SKILL.md（`~/.claude/skills/draw/`），Codex 沒有 Skill 機制，改用「Python 腳本 + 全域 AGENTS.md 觸發規則」達成同樣效果。

---

## 你需要手動準備的三件事

### ① OpenAI 帳號 + 儲值（US$5 起跳）

> 💡 如果你已經在用 Codex CLI（特別是用 ChatGPT 帳號登入），這步驟通常已經完成了。如果用 OpenAI API Key 登入 Codex，那 API 計費已經設好，只要確認 Billing 有餘額即可。

1. 到 [platform.openai.com](https://platform.openai.com) 登入
2. Billing → Add to credit balance → 至少 US$5（約 NT$160）

### ② 組織 Individual 驗證

gpt-image-2 需要身份驗證。

1. 到 [platform.openai.com/settings/organization/general](https://platform.openai.com/settings/organization/general)
2. Verifications → Verify Organization
3. 上傳身分證正反面 + 手持自拍
4. 等 ~15 分鐘驗證通過

> 沒做這步生圖會跳 `403 Organization must be verified`。

### ③ 取得 API Key

1. 到 [platform.openai.com/api-keys](https://platform.openai.com/api-keys)
2. Create new secret key → 名稱 `codex-draw` → Permissions: All → Create
3. 複製 `sk-...` Key

---

## 把以下內容整份丟給 Codex CLI

> 開 Codex CLI，貼這整個檔案內容，Codex 會自動執行並提示貼 API Key。

---

# 🤖 Codex 執行區（以下由 AI 自動完成）

你是 Codex CLI 助手，請依序完成以下任務。

## 任務 1：環境檢查

```bash
python --version
pip --version
```

沒 Python → 提示安裝 Python 3.10+。

## 任務 2：安裝 openai 套件

```bash
pip install openai --break-system-packages
```

Windows 跳過 `--break-system-packages`；macOS/Linux 加上。

## 任務 3：儲存 API Key

問使用者：「請貼上你剛從 OpenAI 複製的 API Key（`sk-...` 開頭）」

拿到 Key 寫入 `~/.openai.env`（**全域可用**）：

```
OPENAI_API_KEY=sk-使用者貼的那串
```

- Windows：`C:/Users/<你>/.openai.env`
- macOS/Linux：`~/.openai.env`

> 注意：這檔案要 600 權限（Unix）或加隱藏屬性（Windows）。
> 在 home 底下，不會被任何專案的 `.gitignore` 規則影響到（也別把 home 當 repo 就好）。

## 任務 4：建立全域 draw 腳本

建立資料夾：

- Windows：`C:/Users/<你>/codex-tools/`
- macOS/Linux：`~/codex-tools/`

在這個資料夾寫入 `draw.py`：

```python
"""
全域生圖腳本（OpenAI gpt-image-2）

用法：
  python draw.py "一隻穿西裝的龍蝦，寫實風格"
  python draw.py "演講海報" --size 1536x1024 --name poster
  python draw.py "把背景換成海底" --edit ./image.png --name edited
  python draw.py "加一頂帽子" --edit ./image.png --mask ./mask.png --name masked

讀 OPENAI_API_KEY 來源（依序）：
  1. 當前 shell 環境變數
  2. 當前工作目錄的 .env
  3. 使用者 home 的 ~/.openai.env

輸出：
  預設「當前工作目錄/slides/generated/」
  若該目錄不存在，會建立「./generated/」
"""

import os
import sys
import base64
import argparse
from pathlib import Path
from datetime import datetime

MODEL = "gpt-image-2"
DEFAULT_SIZE = "1024x1024"
DEFAULT_QUALITY = "low"
DEFAULT_N = 1


def load_env_from_file(path: Path):
    if not path.exists():
        return
    with open(path, encoding="utf-8") as f:
        for line in f:
            line = line.strip()
            if line and not line.startswith("#") and "=" in line:
                key, value = line.split("=", 1)
                os.environ.setdefault(key.strip(), value.strip().strip('"').strip("'"))


def load_env():
    load_env_from_file(Path.cwd() / ".env")
    load_env_from_file(Path.home() / ".openai.env")


def resolve_outdir(user_outdir):
    if user_outdir:
        return Path(user_outdir)
    cwd = Path.cwd()
    slides_dir = cwd / "slides"
    if slides_dir.exists():
        return slides_dir / "generated"
    return cwd / "generated"


def _save_results(result, name, n, outdir):
    stamp = datetime.now().strftime("%Y%m%d_%H%M%S")
    saved = []
    for i, item in enumerate(result.data):
        suffix = f"_{i + 1}" if n > 1 else ""
        out_path = outdir / f"{name}_{stamp}{suffix}.png"
        png_bytes = base64.b64decode(item.b64_json)
        out_path.write_bytes(png_bytes)
        saved.append(out_path)
        print(f"  [OK] {out_path}")
    return saved


def draw(prompt, size, quality, n, name, outdir):
    from openai import OpenAI
    if not os.getenv("OPENAI_API_KEY"):
        print("錯誤：找不到 OPENAI_API_KEY", file=sys.stderr)
        sys.exit(1)
    outdir.mkdir(parents=True, exist_ok=True)
    client = OpenAI()
    print(f"畫圖中（{MODEL}, {size}, {quality}, n={n}） -> {outdir}", file=sys.stderr)
    result = client.images.generate(model=MODEL, prompt=prompt, size=size,
                                     quality=quality, n=n)
    return _save_results(result, name, n, outdir)


def edit(prompt, image_path, mask_path, size, quality, n, name, outdir):
    from openai import OpenAI
    if not os.getenv("OPENAI_API_KEY"):
        print("錯誤：找不到 OPENAI_API_KEY", file=sys.stderr)
        sys.exit(1)
    if not image_path.exists():
        print(f"錯誤：找不到來源圖片 {image_path}", file=sys.stderr)
        sys.exit(1)
    outdir.mkdir(parents=True, exist_ok=True)
    client = OpenAI()
    mode = "遮罩改圖" if mask_path else "全圖改圖"
    print(f"改圖中（{mode}, {MODEL}, {size}, {quality}） -> {outdir}", file=sys.stderr)
    kwargs = dict(model=MODEL, image=open(image_path, "rb"),
                  prompt=prompt, size=size, quality=quality, n=n)
    if mask_path:
        if not mask_path.exists():
            print(f"錯誤：找不到遮罩圖片 {mask_path}", file=sys.stderr)
            sys.exit(1)
        kwargs["mask"] = open(mask_path, "rb")
    result = client.images.edit(**kwargs)
    return _save_results(result, name, n, outdir)


def main():
    load_env()
    parser = argparse.ArgumentParser()
    parser.add_argument("prompt", nargs="+")
    parser.add_argument("--edit", default=None)
    parser.add_argument("--mask", default=None)
    parser.add_argument("--size", default=DEFAULT_SIZE)
    parser.add_argument("--quality", default=DEFAULT_QUALITY,
                        choices=["low", "medium", "high", "auto"])
    parser.add_argument("--n", type=int, default=DEFAULT_N)
    parser.add_argument("--name", default="image")
    parser.add_argument("--outdir", default=None)
    args = parser.parse_args()
    prompt = " ".join(args.prompt)
    outdir = resolve_outdir(args.outdir)
    if args.edit:
        edit(prompt, Path(args.edit), Path(args.mask) if args.mask else None,
             args.size, args.quality, args.n, args.name, outdir)
    else:
        draw(prompt, args.size, args.quality, args.n, args.name, outdir)


if __name__ == "__main__":
    main()
```

## 任務 5：在全域 AGENTS.md 加觸發規則

編輯 `~/.codex/AGENTS.md`（不存在就建），加入以下段落：

````markdown
## 生圖能力（gpt-image-2）

當使用者說：
- 「畫一張 XX」「生一張圖」「做一張圖」
- 「畫個封面／插圖／示意圖／分鏡」
- 「產生圖片」「幫我生圖」
- 「改這張圖」「修改圖片」「把背景換成 XX」（→ 改圖模式，需提供圖片路徑）

請執行：

```bash
python ~/codex-tools/draw.py "<使用者要的內容>" --name <檔名前綴>
```

Windows 路徑：`python C:/Users/<你>/codex-tools/draw.py "..." --name ...`

### 參數
- `prompt`（必填）：要畫什麼
- `--size`：`1024x1024`（方，預設）/ `1536x1024`（橫）/ `1024x1536`（直）
- `--quality`：`low`（預設，NT$0.3）/ `medium`（NT$1.3）/ `high`（NT$5.5）
- `--n`：生成張數 1–8
- `--name`：檔名前綴
- `--outdir`：輸出目錄
- `--edit IMAGE_PATH`：改圖模式
- `--mask MASK_PATH`：遮罩（搭配 --edit）

### 判斷 quality 的原則
**預設永遠用 `low`**（省錢 + 速度優先）

- **low**（NT$0.3）：99% 情境。演講簡報、教學插圖、封面、demo 都夠
- **medium**（NT$1.3）：通常不用
- **high**（NT$5.5）：實體印刷、跨語言文字零錯才用

不確定就 **low**，不要自作主張升級。

### 錯誤處理
- `403 Organization must be verified` → 到 platform.openai.com/settings/organization/general 做 Individual 驗證
- `401 Invalid API key` → 檢查 `~/.openai.env`
- `429 Rate limit` → 額度用完，到 Billing 儲值

### 輸出
PNG 檔，格式：`<name>_<YYYYMMDD_HHMMSS>.png`，存於：
- `<cwd>/slides/generated/`（若 `slides/` 存在）
- 否則 `<cwd>/generated/`
````

## 任務 6：驗證安裝

```bash
python ~/codex-tools/draw.py "一隻可愛的黑貓，扁平插畫風格" --name test
```

預期：
- 終端機顯示 `[OK] .../generated/test_<時間戳>.png`
- 開啟該檔能看到黑貓圖

錯誤：
- `403 Organization must be verified` → 引導到 platform.openai.com/settings/organization/general 驗證
- `401 Invalid API key` → 檢查 `~/.openai.env` 的 Key
- `429 Rate limit` → 提示到 Billing 儲值

## 任務 7：回報結果

> 🎉 安裝完成！
>
> 以後在任何專案裡，對我說「畫一張 XX」就能生圖。
>
> - 預設存 `slides/generated/` 或 `./generated/`
> - 每張約 NT$0.3（low 品質）
> - 模型：gpt-image-2

---

# 📚 原理延伸

## 為什麼用「腳本 + AGENTS.md」而不是 MCP？

| 方式 | 優點 | 缺點 |
|---|---|---|
| **腳本（本篇）** | 零依賴、按需呼叫、不佔資源、好除錯 | 需要 AGENTS.md 裡寫觸發規則 |
| MCP Server | 結構化工具、IDE 也能用 | 要常駐、設定較繁 |

對 Codex 來說，因為沒有 Skill 機制，「全域腳本 + 全域 AGENTS.md 規則」是最接近 Claude Code Skill 體驗的做法。

## 安全性

- API Key 在 `~/.openai.env`，跟專案 repo 完全分離
- 不會被 git 追蹤
- 生圖在本機執行，不經第三方伺服器

## 成本控制

1. **預設 low 品質**：NT$0.3/張
2. **AGENTS.md 規則**：Codex 不會自作主張升級
3. **透明計費**：到 [platform.openai.com/usage](https://platform.openai.com/usage) 查用量

---

## 相關資源

- [OpenAI API 文件](https://platform.openai.com/docs/guides/images)
- [gpt-image-2 模型介紹](https://platform.openai.com/docs/models/gpt-image-2)
- [Codex AGENTS.md 文件](https://developers.openai.com/codex/guides/agents-md)
- [00 環境建置](00-環境建置.md)（Python 等基礎工具）

---

**作者**：三師爸（宋睿偉）
**頻道**：[三師爸 Sense Bar](https://youtube.com/@SenseBar)
**最後更新**：2026-04-26
