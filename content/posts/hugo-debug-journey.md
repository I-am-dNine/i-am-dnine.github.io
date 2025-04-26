+++
title = "從 Page Not Found 到 Hello World：我用 Hugo 建站卡關又解鎖的一天"
date = 2025-04-26T14:56:59+08:00
draft = false
[ author ]
  name = "D9"
+++

## 從 Page Not Found 到 Hello World：我用 Hugo 建站卡關又解鎖的一天

> 本文記錄我在使用 [Hugo](https://gohugo.io) 靜態網站生成器時，從「頁面空白」到「成功看到文章」的完整排錯歷程。特別適合第一次使用 Hugo 主題（像 `hello-friend-ng`）卻一直看不到畫面的你！

---

## ☀️ 起點：設定 GitHub Pages 部落格

我用以下流程建立 Hugo 網站並使用 `hello-friend-ng` 主題：

```bash
hugo new site d9-site
cd d9-site
git init
git submodule add https://github.com/rhazdon/hugo-theme-hello-friend-ng.git themes/hello-friend-ng
```

---
接著在 `config.toml` 設定主題名稱與一些基本設定：

```bash
theme = "hello-friend-ng"
baseURL = "http://localhost:1313/"
title = "Baron's Dev Blog"
languageCode = "en-us"
```

並建立一篇文章：
```bash
hugo new posts/hello-world.md
```

將內容修改如下：
```bash
+++
title = "Hello World"
date = 2025-04-23T23:08:59+08:00
draft = false
+++

This is my first Hugo post using hello-friend-ng theme!
```

---
## 🧱 問題來了：Page Not Found

當我輸入指令：

```bash
hugo server -D
```

瀏覽器打開 `http://localhost:1313`，卻顯示 **Page Not Found** ❌

---
## 🧨 更慘的是：我自己蓋掉了主題的 layout

在 debug 過程中，我聽從了建議（🤦）自行建立了這個檔案：
```bash
layouts/_default/baseof.html
```

內容如下：
```bash
{{ define "main" }}
  <main>
    <h1>Recent Posts</h1>
    <ul>
      {{ range where .Site.RegularPages "Section" "posts" }}
        <li><a href="{{ .RelPermalink }}">{{ .Title }}</a></li>
      {{ end }}
    </ul>
  </main>
{{ end }}
```
但這其實是錯的！
### ❗️Hugo 的行為是：

> **當你有 layouts/ 資料夾時，會優先於主題的 layouts。**

因此我這段簡陋的 `baseof.html` 直接蓋掉了 `hello-friend-ng` 主題本來精心設計好的整個頁面結構，導致畫面什麼都沒有（空白頁）。

---
## 🔧 解法：刪除 layouts、使用主題自帶設定

### ✅ 正確作法：

1. **刪除 layouts 資料夾：**
  ```bash
    rm -rf layouts/
    ```

2. **使用主題 exampleSite 設定：**
    建議從主題的 `exampleSite/config.toml` 複製一份起來再改。它已經設定好主題參數與顯示邏輯。
    
3. **執行時明確指定主題（可選）：**
```bash
    hugo server -D --theme hello-friend-ng
```

4. **確認有文章，且 draft = false**
---
## 🎉 結果：網站終於跑起來了！

當一切配置正確、layouts 沒有被我亂加之後，`http://localhost:1313` 終於成功顯示：
- 網站標題與首頁
- Recent Posts 列表
- 我的 Hello World 文章
---
## 💡 小結：學會的五件事

| 教訓                                    | 原因                       |
| ------------------------------------- | ------------------------ |
| ❌ 不要亂建 layouts/                       | Hugo 會優先使用，主題 layout 被蓋掉 |
| ✅ 使用主題的 exampleSite 設定當範本             | 預設參數與頁面邏輯較完整             |
| ✅ `draft = false` 要記得設                | 否則文章不會顯示                 |
| ✅ `hugo server -D` 加 `--theme` 可以強制指定 | 避免抓錯主題                   |
| ✅ 多用 `hugo list all` 看哪些頁面被正確解析       | 排錯神器！                    |

---
## 🧩 附註：為何我會做錯？

因為一開始誤判主題沒生效，想自己補個 layout 測試，結果反而讓主題失效。這也是 Hugo 靜態網站的「靈活性 vs 陷阱」：**你有完全自由，但也可能自找麻煩。**

---
希望這篇紀錄能幫助也正在「空白頁地獄」的人，早日脫離困境 🙌
