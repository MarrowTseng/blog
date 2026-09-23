# Dowhu's Blog

Hugo + PaperMod 做的個人技術部落格，部署在 <https://blog.島呼.tw>。

---

## 目錄

- [整個流程長怎樣](#整個流程長怎樣)
- [檔案在幹嘛](#檔案在幹嘛)
- [日常操作](#日常操作)
  - [發一篇新文章](#發一篇新文章)
  - [改 About 頁面](#改-about-頁面)
  - [換大頭貼](#換大頭貼)
  - [在文章裡放圖片](#在文章裡放圖片)
- [Commit 訊息怎麼取名](#commit-訊息怎麼取名)
- [本機預覽](#本機預覽)
- [遇到問題](#遇到問題)

---

## 整個流程長怎樣

```
你在本機改原始檔
        ↓  git commit   （存檔到本機，GitHub 上還沒變）
        ↓  git push     （上傳到 GitHub）
GitHub Actions 自動跑 hugo 建置
        ↓
GitHub Pages 更新 → https://blog.島呼.tw
```

**重點：`public/` 資料夾不要管它。** 那是 Hugo 建置出來的產物，GitHub Actions 會在它自己的機器上重新建一份，所以 repo 裡不需要。`.gitignore` 已經把它排除了。

**所有改動都要 commit + push 才會上線。** 只 commit 不 push 的話，網站不會有任何變化。

推上去之後大約 **30 秒**建置完成，再等一下下 CDN 快取更新。如果看到的還是舊的，按 <kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>R</kbd> 強制重新整理。

建置狀況可以在這裡看：<https://github.com/MarrowTseng/blog/actions>

---

## 檔案在幹嘛

### 你會常常動到的

| 路徑 | 做什麼 |
|---|---|
| `content/posts/` | **文章放這裡**，一篇一個 `.md` 檔 |
| `content/about.md` | About 頁面的內容 |
| `hugo.toml` | 網站設定：標題、作者、選單、社群連結、程式碼高亮主題等等 |
| `static/images/` | 圖片、大頭貼。放這裡的檔案會原封不動被複製到網站根目錄 |

### 偶爾會動到的

| 路徑 | 做什麼 |
|---|---|
| `archetypes/default.md` | 新文章的範本。跑 `hugo new` 時會用這個檔產生 front matter |
| `assets/css/extended/custom.css` | 自訂樣式。改配色、版面寬度、控制器外觀都在這 |
| `content/archives.md` | 歸檔頁（只是一個空殼，內容由主題自動產生） |
| `content/search.md` | 搜尋頁（同上） |

### 最好別亂動的

| 路徑 | 做什麼 |
|---|---|
| `layouts/_partials/extend_footer.html` | 像素小遊戲的全部程式碼（駭客、桌機、無人機砲台、飛彈、邪惡機器人）＋ logo 的假終端機 |
| `layouts/_partials/matrix_rain.html` | 背景的注音文字雨 |
| `layouts/_partials/header.html` | 自訂頁首（放了終端機輸入框） |
| `layouts/_partials/home_info.html` | 首頁的個人卡片（頭像、名字、一句話） |
| `themes/PaperMod/` | 主題本體，git submodule。**不要改這裡面的東西**，要客製化就在 `layouts/` 或 `assets/` 覆寫 |

### 設定檔

| 路徑 | 做什麼 |
|---|---|
| `.github/workflows/hugo.yml` | GitHub Actions 的部署設定 |
| `static/CNAME` | 自訂網域。內容就一行 `blog.島呼.tw` |
| `.gitignore` | 叫 git 忽略 `public/` 等建置產物 |
| `.gitmodules` | 記錄 PaperMod 主題的來源 |
| `.claude/launch.json` | 本機預覽伺服器的設定 |

### 不用管的

| 路徑 | 說明 |
|---|---|
| `public/` | Hugo 建置產物，已被 git 忽略。刪掉也沒關係，下次建置會重生 |
| `resources/` | Hugo 的快取，同上 |
| `.hugo_build.lock` | Hugo 執行時的鎖定檔，同上 |

---

## 日常操作

### 發一篇新文章

```bash
hugo new posts/my-first-ctf-writeup.md
```

會在 `content/posts/` 產生檔案，裡面已經填好 front matter：

```yaml
---
title: "My First Ctf Writeup"
date: 2026-09-23T16:09:23+08:00
draft: true
description: ""
summary: ""
categories: []
tags: []
cover:
  image: ""
  alt: ""
  relative: true
ShowToc: true
TocOpen: false
---
```

各欄位的意思：

| 欄位 | 說明 |
|---|---|
| `title` | 文章標題。從檔名自動產生，通常要自己改成中文 |
| `date` | 發布日期，決定文章排序 |
| `draft` | **`true` 代表草稿，不會上線。寫完要改成 `false`** |
| `description` | 給搜尋引擎看的敘述 |
| `summary` | 文章列表上顯示的摘要。不填的話會自動抓開頭幾行 |
| `categories` | 分類，例如 `["資安"]` |
| `tags` | 標籤，例如 `["CTF", "Web", "SQL Injection"]` |
| `cover.image` | 封面圖，填圖片檔名 |
| `ShowToc` | 是否顯示目錄 |
| `TocOpen` | 目錄預設展開還是收合 |

**檔名建議用英文小寫加連字號**，因為檔名會直接變成網址：

```
content/posts/hitcon-2026-web-writeup.md
    → https://blog.島呼.tw/posts/hitcon-2026-web-writeup/
```

用中文檔名的話網址會變成一長串編碼，不好分享。標題寫中文沒問題，那是 `title` 欄位的事。

寫完之後：

```bash
git add .
git commit -m "Add HITCON 2026 web writeup"
git push
```

> **忘記把 `draft` 改成 `false` 的話文章不會出現。** 這是最常見的狀況 —
> 推上去卻找不到文章，先檢查這個。本機預覽看得到是因為預覽有開 `-D`
> 參數會顯示草稿，正式建置則會跳過。

### 改 About 頁面

直接編輯 `content/about.md`。`---` 中間那段是設定，不用動；下面才是內容，用 Markdown 寫：

```markdown
---
title: "About"
url: "/about/"
summary: "關於 Dowhu"
hidemeta: true
ShowToc: false
---

## Hi, I'm Dowhu

臺科大資工，專注於 Web 安全與逆向工程。

### 經歷
- 某某 CTF 戰隊成員
- ...
```

然後一樣 commit + push。

### 換大頭貼

1. 把新圖片放進 `static/images/`
2. 如果**檔名不是** `avatar.svg`，要去 `hugo.toml` 改這一行：

```toml
[params.homeInfoParams]
  imageUrl = "images/avatar.png"   # 改成你的檔名
```

3. commit + push

圖片建議正方形，200×200 以上就夠了（顯示尺寸是 120×120，但要考慮高解析度螢幕）。

### 在文章裡放圖片

把圖片放進 `static/images/`，在文章裡這樣寫：

```markdown
![說明文字](/images/my-screenshot.png)
```

路徑開頭的 `/` 是指網站根目錄。`static/` 底下的東西會被直接複製到根目錄，所以 `static/images/x.png` 對應到網址 `/images/x.png`。

---

## Commit 訊息怎麼取名

不用想太複雜，**看得懂就好**。原則是「這個 commit 做了什麼」，用祈使句開頭：

```bash
git commit -m "Add HITCON 2026 web writeup"
git commit -m "Write the about page"
git commit -m "Replace the placeholder avatar"
git commit -m "Fix broken image link in the SQLi post"
```

常用的開頭動詞：

| 動詞 | 用在 |
|---|---|
| `Add` | 新增文章、新增圖片 |
| `Update` | 更新既有內容 |
| `Fix` | 修正錯字、壞掉的連結 |
| `Remove` | 刪掉東西 |
| `Write` | 寫了原本空白的內容 |

如果一次改很多東西，就分成好幾個 commit，一個 commit 做一件事。這樣之後要回溯比較容易。

想看之前的紀錄：

```bash
git log --oneline
```

---

## 本機預覽

寫文章的時候開著預覽比較方便，存檔後瀏覽器會自動重新整理。

```bash
hugo server -D
```

開 <http://localhost:1313>。`-D` 是顯示草稿的意思，沒有這個參數就看不到 `draft: true` 的文章。

按 <kbd>Ctrl</kbd>+<kbd>C</kbd> 停止。

---

## 遇到問題

### 推上去了但網站沒變

1. 先去 <https://github.com/MarrowTseng/blog/actions> 看建置有沒有成功（綠色勾勾）
2. 建置成功的話，按 <kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>R</kbd> 強制重新整理，避開快取
3. 還是不行的話等個幾分鐘，GitHub Pages 的 CDN 有時比較慢

### 文章不見了

檢查 front matter 裡的 `draft` 是不是還是 `true`。

### 本機預覽改了東西沒反應

Hugo 的開發伺服器偶爾會卡住（改了 `layouts/` 底下的檔案時比較容易發生），停掉重開就好。

### 建置出現 deprecated 警告

```
WARN  deprecated: .Language.LanguageCode was deprecated in Hugo v0.158.0
WARN  deprecated: .Language.LanguageDirection was deprecated in Hugo v0.158.0
```

這兩個來自 PaperMod 主題本身，不是你的設定造成的，目前不影響運作。等主題更新就會消失，要更新主題的話：

```bash
git submodule update --remote themes/PaperMod
```

（更新主題有機會讓客製化的部分跑版，更新後記得先本機預覽確認再 push。）

### 不小心改壞了想還原

還沒 commit 的話：

```bash
git checkout <檔案路徑>        # 還原單一檔案
git checkout .                 # 還原全部
```

已經 commit 但還沒 push：

```bash
git reset --soft HEAD~1        # 取消最後一個 commit，改動保留
```

已經 push 了的話，先別急著動手，`git log --oneline` 看一下紀錄再決定。
