# ChuaLab · 静态工具站

**线上地址：<https://robot-chua.github.io/>** · 仓库：<https://github.com/Robot-Chua/Robot-Chua.github.io>

个人安卓游戏工具站（ChuaLab）。纯 HTML / CSS / JS，**零依赖、零构建**，`index.html` 双击就能看，也可以直接扔到 GitHub Pages。

推送 `main` 分支后 GitHub Pages 会自动重新构建，几十秒后线上生效。

## 目录结构

```
chualab/
├─ index.html              主页（首屏 / 工具卡片 / 安装教程 / 权限说明 / FAQ / 关于 / 页脚）
├─ 404.html                404 页（GitHub Pages 会自动使用）
├─ assets/
│  ├─ style.css            全部样式（顶部 :root 里是配色变量）
│  ├─ app.js               交互：顶栏滚动态、移动端菜单、导航高亮、渐显、点击复制
│  ├─ logo.svg             站点图标 / 顶栏 logo
│  └─ og.png               社交分享预览图（1200×630）
├─ downloads/              要对外下载的 APK 放这里
│  ├─ VClicker-v1.4.apk            （81 KB，Android 7.0+）
│  └─ ZonghengHelper-v2.2.apk      （73 KB，Android 11+）
├─ .nojekyll               告诉 GitHub Pages 别跑 Jekyll
└─ README.md               本文件（可以留在仓库里，不影响页面）
```

## 本地预览

直接双击 `index.html` 即可（全部是相对路径，`file://` 下也能正常下载）。

想用「像线上一样的 http 环境」测试，可以起一个静态服务器：

```powershell
python -m http.server 8000
# 然后打开 http://127.0.0.1:8000
```

## 部署（已完成）

仓库是 **`Robot-Chua/Robot-Chua.github.io`**（GitHub 用户站点仓库），Pages 从 `main` 分支根目录自动发布，所以线上地址就是 **<https://robot-chua.github.io/>**，不需要在 Settings 里再配置。

改完内容后只要：

```bash
cd chualab
git add -A
git commit -m "update: 说明这次改了什么"
git push
```

推送后 GitHub Pages 会自动重建，通常 30 秒内生效（可以在仓库的 Pages 设置里看构建状态）。

> 已经用 Chua 的 GitHub 账号通过 Git Credential Manager 授权过一次，凭据存在 Windows 凭据管理器里，之后的 `git push` 不会再弹授权。

### 绑定自己的域名（可选）

1. 在 `chualab/` 下建一个 `CNAME` 文件，内容就是裸域名，例如 `chualab.example.com`。
2. 域名 DNS 加一条 `CNAME` 记录指向 `robot-chua.github.io`。
3. 仓库 Settings → Pages → Custom domain 填同一个域名，勾选 Enforce HTTPS。
4. 域名生效后，把 `index.html` 里的 `canonical` / `og:url` / `og:image` 三处 `robot-chua.github.io` 换成新域名。

## 日常怎么改

### 发新版本 APK

1. 把新 APK 放进 `downloads/`（文件名带版本号，例如 `VClicker-v1.5.apk`）。
2. 在 `index.html` 里改这一张卡片的三处：
   - 标签：`<span class="tag is-accent">v1.4</span>` → `v1.5`
   - 下载按钮：`href="downloads/VClicker-v1.4.apk"` 和 `<span class="btn-meta">81 KB</span>`
   - `<details class="changelog">` 里的更新内容，以及 `SHA-256` 那行
3. 顺手删掉 `downloads/` 里的旧包（或用 Git 历史留档）。

算校验值和看体积：

```powershell
Get-FileHash .\downloads\VClicker-v1.5.apk -Algorithm SHA256 | Select-Object -ExpandProperty Hash
"{0:N1} KB" -f ((Get-Item .\downloads\VClicker-v1.5.apk).Length / 1KB)
```

### 上第三个工具（现在的「新工具」占位卡）

把 `index.html` 里 `<article class="card is-soon reveal">` 那一段整块替换成一张正常卡片即可，
最省事的做法是**复制一张现有卡片**再改：

```html
<article class="card reveal">
  <div class="card-head">
    <div class="tool-icon" aria-hidden="true"><svg ...>你的图标</svg></div>
    <div class="tool-title">
      <h3>工具名</h3>
      <p class="sub">一句话定位 · 包名</p>
    </div>
  </div>
  <div class="tag-row">
    <span class="tag is-accent">v1.0</span>
    <span class="tag">xx KB</span>
    <span class="tag">Android 8.0+</span>
  </div>
  <p class="card-desc">这个工具干什么用的。</p>
  <ul class="feature-list">
    <li><b>要点</b>：说明</li>
  </ul>
  <details class="changelog">
    <summary>v1.0 更新内容</summary>
    <div class="changelog-body">
      <ul><li>首发</li></ul>
      <code class="hash">SHA-256 ...</code>
    </div>
  </details>
  <div class="card-foot">
    <a class="btn btn-primary btn-block" href="downloads/你的工具-v1.0.apk" download>
      下载 APK<span class="btn-meta">xx KB</span>
    </a>
  </div>
</article>
```

别忘了首屏的统计数字（`2 个已上线工具`）和 `JSON-LD` 里的 `SoftwareApplication` 也要加一条。

### 改配色 / 站点信息

- 配色：`assets/style.css` 顶部 `:root` 里的 `--bg / --panel / --cyan / --violet / --text` 等变量。
- 站点名：`index.html` 里所有 `Chua<b>Lab</b>`、`<title>`、`og:*`。
- 联系方式：`#about` 里三个 `<button class="contact-row" data-copy="...">`，改 `data-copy` 和显示的 `.val` 即可（点击复制会自动跟着变）。

## 注意事项

- GitHub 单文件上限 100 MB，Pages 仓库建议 < 1 GB。工具都是几十 KB，随便放。
- 别把 `.apk` 加进 `.gitignore`；也要确认不是用 Git LFS（Pages 不解析 LFS 指针）。
- APK 是自签名包，浏览器/微信内置浏览器下载后可能提示「文件可能有风险」，这是正常的，页面 FAQ 里已解释。
- 本站没有后端，所以没有访问统计 —— 想加的话推荐不自带 Cookie 的轻量方案，或干脆不加。
