# Codex 懶人包 #02：連接 GitHub

> 版本：v0.1（Codex 版）
> 更新日期：2026-04-26

> 📌 **本懶人包可獨立執行**：會自動檢查並安裝所需工具。

---

## 這個懶人包會幫你做什麼？

讓 Codex 能夠直接操控 GitHub：
- 建立 GitHub repo
- 把 Codex 做好的網頁、教材推上線
- 開啟 GitHub Pages 讓學生掃 QR Code 開啟
- 之後一句話就能上線

> 💡 **GitHub 部分跟 Claude Code 版完全一樣**——`gh` CLI 是系統工具，跟用哪個 AI agent 無關。

---

## 先備條件

- [ ] Codex CLI 已安裝且能用
- [ ] 有 email 信箱（沒 GitHub 帳號的話，懶人包會引導註冊）
- [ ] 電腦有網路連線

---

## 請 Codex 幫我執行以下步驟

### 步驟零：環境檢查

1. 作業系統
2. 網路
3. `git --version`，沒有就裝（Win: `winget install --id Git.Git`；mac: `xcode-select --install`；Linux: `sudo apt install git`）
4. `gh --version`，沒有就裝（Win: `winget install --id GitHub.cli`；mac: `brew install gh`）
5. `git config --global user.name`：是否已設

---

### 步驟零.五：確認 GitHub 帳號

> 🖐️ Codex 先問：「你有 GitHub 帳號了嗎？」

**已有** → 跳到步驟一。

**沒有** → 引導註冊：
1. 開 https://github.com/signup
2. 填 Email、Password（≥15 字元 或 ≥8 字元含數字+小寫）、Username（建議英文+數字，例 `mathteacher2026`）
3. 通過拼圖驗證、點 Create account
4. 收信、貼驗證碼
5. 跳過 onboarding 問卷，選 Free 方案
6. 完成

> ⚠️ Username 不好改（GitHub Pages 網址會變），慎選。

---

### 步驟一：登入 GitHub

```bash
gh auth status
```

未登入：
```bash
gh auth login --web --git-protocol https
```

> 🖐️ 終端機會顯示驗證碼；瀏覽器會開 GitHub 授權頁，貼驗證碼後 Authorize。

確認：`gh auth status`

---

### 步驟二：設定 Git 使用者資訊

```bash
git config --global user.name
git config --global user.email
```

如未設定：
```bash
git config --global user.name "你的姓名"
git config --global user.email "你的email"
```

---

### 步驟三：建立第一個測試 repo 並驗證

1. 建臨時資料夾 `github-test`
2. `git init`
3. 建 `index.html`，內容「Hello！GitHub 連接成功！」
4. 推上去 + 開 Pages：

```bash
gh repo create github-test --public --source=. --push
gh api repos/{owner}/github-test/pages -X POST -f build_type=workflow -f source.branch=main -f source.path=/
```

5. 等 1 分鐘左右
6. 取得 Pages 網址告知使用者
7. 🖐️ 使用者開瀏覽器確認

---

### 步驟四：清除測試 repo（可選）

> 🖐️ 詢問：「要保留測試 repo 還是刪除？」

刪除：
```bash
gh repo delete github-test --yes
```

並刪本地 `github-test` 資料夾。

> ✅ 「全部完成！以後說『推到 GitHub 上線』，Codex 就會幫你做好。」

---

## 完成！接下來這樣用

| 你說的話 | Codex + GitHub 會做的事 |
|----------|-------------------------|
| 「做一個互動遊戲推到 GitHub 上線」 | 產生 HTML → 建 repo → push → 開 Pages → 給網址 |
| 「更新這個網頁的內容」 | 改檔 → push → 自動更新 |
| 「產生這個網頁的 QR Code」 | 產生 QR Code 圖片 |
| 「刪除這個 repo」 | 刪 GitHub 上的 repo |

---

## 如果失敗

對 Codex 說：「GitHub 懶人包執行失敗，幫我檢查重新處理。」

完全重置：
```bash
gh auth logout
gh auth login --web --git-protocol https
```

---

## 常見問題

| 問題 | 解法 |
|------|------|
| `gh: command not found` | 重啟終端機；確認安裝路徑在 PATH |
| `git: command not found` | 重啟終端機 |
| Pages 顯示 404 | 等 1-2 分鐘再重整，部署需時間 |
| push 被拒 | 確認 `gh auth status` 顯示 `repo` 權限 |
