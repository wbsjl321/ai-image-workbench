# 雨霖霖 落地页 — 部署与维护说明

本目录是**雨霖霖**（仓库名 `ai-image-workbench`）Windows 桌面客户端的下载落地页。

> **一句话**：`index.html` 是**纯静态单文件**（HTML + CSS + JS 全在里面），双击就能看，
> **不需要构建、不需要 Node、不需要数据库**。

---

## 1. 目录内容

### 必须上传（四样）

| 文件 | 说明 |
| --- | --- |
| `index.html` | 落地页本体，HTML + CSS + JS 全在这一个文件里，不引 CDN、不引字体。 |
| `app-icon.png` | 应用图标（1024×1024，来自源码仓库 `electron/icon.png`），同时用作顶栏品牌图标和浏览器 favicon。 |
| `hero-bg.jpg` | **首屏背景图**（2560×1400，暖纸「黎明」山脊）。漏传首屏会退化成纯色底，虽然不影响阅读，但会明显变单调。详见下面「首屏背景图」。 |
| `shots/` | 页面用到的全部图片，见下表。**漏传会导致首屏窗口截图和截图墙开天窗。** |

### `shots/` 里的图

| 文件 | 用途 | 尺寸 |
| --- | --- | --- |
| `ui-gen.png` | 生图工作台截图（**首屏右侧窗口 + 截图墙**，用得最多） | 1600×1075 |
| `ui-agent.png` | Agent 模式截图 | 1600×1075 |
| `ui-video.png` | 视频创作台截图 | 1600×1075 |
| `ui-motion.png` | 动图生成截图 | 1600×1075 |
| `ui-reverse.png` | 反推提示词截图 | 1600×1075 |
| `ui-prompts.png` | 提示词库截图 | 1600×1075 |
| `shot-*.jpg`、`hero-sample.jpg` | **早期版本用过的图，现已不被页面引用**，可以删。 | — |

### 首屏背景图 `hero-bg.jpg`

首屏（`.hero`）的 `background-image` 直接指向 `hero-bg.jpg`，配 `background-size:cover`、
`background-position:50% 72%`。上面叠了两层 `::before` / `::after`：

- `::before` —— 从上到下的纸色柔光罩。**它的作用是保证左上正文区够干净**，同时保住底部山脊的层次；
  如果你换了别的背景图发现文字发糊，调这一层的透明度就行。
- `::after` —— 76px 的细网格，透明度只有 0.032，径向遮罩四面渐隐，只当纸纹用。

背景图**不是照片素材，是程序画的**，生成脚本在 `_schemes/_mkherobg.py`：

```bash
python _schemes/_mkherobg.py     # 重绘并覆盖 landing/hero-bg.jpg
```

脚本要点（改配色时注意）：

- 天空是分段线性渐变，地平线压到 `ny≈0.62` 附近转暖；
- 云带用多条横向 + 1D fbm 调制的高斯带取 `max` 合成，再高斯模糊 14px；
- 山脊是 1D fbm（多个正弦按 2.07 倍频叠加）生成轮廓线，**两层分别用不同亮度做空气透视**——
  远层更亮更淡，近层更深，这样才有纵深，不会变成剪影贴纸；
- 最后叠一层轻暗角 + σ=1.6 的细颗粒防色带。

**踩过的坑**：一开始用「纯高斯柔光叠加」做背景，振幅只有 10~30 压在 236 的底色上，
2560px 跨度里只有 27 级差，肉眼完全读不出结构，出来就是一片均匀奶油白。
**做背景必须要有可见尺度上的形体**（山脊/云/地平线），光靠柔光叠加做不出来。


### 不要上传

| 文件 | 说明 |
| --- | --- |
| `README.md` | 本文件。留在本地即可，不想被访问也可以传（不影响页面）。 |
| `_preview-*.png` | 开发时拍的页面渲染截图，**不是产品素材**。 |
| `_ref-multica-*.png` | multica.ai 对标参考图。 |
| `_real-bodies.json` | 导出各版本更新说明时的中间数据，已注入 `index.html`，留档备查即可。 |

### 视觉规格（供后续改版参考）

设计 token 集中在 `index.html` 的 `:root`，改风格只改变量：

| 维度 | 当前取值 |
| --- | --- |
| 内容最大宽度 | `--w: 1180px` |
| 底色（暖纸） | `--paper: #f5f3ee`；次级区 `--paper-2: #eceae3`；卡片 `--card: #fffdf9` |
| 文字三级 | `--ink: #171a18` / `--ink-2: #585c56` / `--ink-3: #696d66` |
| 描边 | `--line: #e3e0d9`；加强描边 `--line-2: #cfccc4`；`--line-strong: #898d86`（只给按钮描边、时间线圆点这类「有意义的图形」） |
| 主色（按钮） | `--primary: #1b3a31`（深绿，悬停 `--primary-h: #12271f`） |
| 强调色 | `--accent: #d9603a`（珊瑚，只做小面积：眉标短线、步骤序号、最新徽标、悬停） |
| 正文字号 | 16px / 行高 1.72 |
| Hero H1 | `clamp(23px, 3.4vw, 46px)`，字重 800，行高 1.24 |
| 区块标题 H2 | `clamp(28px, 3.5vw, 44px)`，字重 800 |
| 区块上下留白 | `section { padding: 96px 0 }` |
| 按钮圆角 | `--r-btn: 8px`；卡片圆角 `--r-card: 12px` |
| 显示字体 | `--display: "Noto Sans SC Black"` 系（本机装了 Noto Sans SC 全字重家族；没有思源宋体，所以不走衬线） |
| 正文/等宽 | `--sans` PingFang SC/Noto Sans SC 系；`--mono` 用于版本号、体积、日期 |
| 断点 | 940px（步骤/特征 4→2 列、首屏两栏→单栏）、820px（截图墙→单列、时间线转竖排）、760px、600px、560px（→单列） |

设计令牌取自 iPolloWork「signal-workspace」设计系统（`deepseek-idesign` 插件自带模板，见 `~/.dsh/profiles/desktop/node_modules/deepseek-idesign/lib/templates/`）。

设计基调：**暖纸底 + 墨字 + 深绿主色 + 珊瑚小面积强调**。不用渐变、不用发光、不用 emoji 当图标、不用蓝色——上一版深色 + 蓝色强调正是被否掉的「AI 味」。首屏右侧放一张带 Windows 标题栏（─ □ ✕）外框的真实界面截图。信息层级靠字号（46 / 44 / 16px）与字色（三级灰）拉开，不靠装饰。

**首屏标题的断行是硬约束**：`<h1>` 里用 `<span class="nb">` 包住「生图 / 画布 / 视频 / 动图 / 同一个窗口」这些不能拆的词，再用 `<br class="brk">` 钉死换行点。这个 `brk` 在**所有宽度都保留**——曾经在窄屏取消它，结果自然换行把顿号甩到行尾、把「动图」挤成孤字。字号 `clamp(23px, 3.4vw, 46px)` 是按「最长一行 11 个字在 320px 窄屏也能放下」反推出来的，不要随手调大。

**深色版之前的迭代**：编辑式（浅底大字号）→ 规格书（浅底 + 网格线）→ 深色工作室 → **暖纸编辑风（当前版）**。前三套完整文件与评分见 `_schemes/` 与 `../qa/ui-review.md`。

### ⚠️ 改版陷阱：JS 生成的 class 也必须被 CSS 覆盖

「更新日志」和「版本时间线」两块是 `renderReleases()` / `renderTimeline()` 用字符串拼出来的，class 名**只出现在 `<script>` 里**。CSS 写漏一个，页面上就是一堆挤在一起没有排版的裸文本。

踩过一次：旧样式表里写的是 `.rel` / `.vt` / `.badge` / `.tl .v` / `.dl2`，而 JS 后来改成了 `.release` / `.tag` / `.latest-badge` / `.tl-ver` / `.tl-date`，两边对不上，整段更新日志塌成 `v1.1.5最新Release v1.1.52026-10-13`。

改这两块样式时，用下面这条命令把 JS 里用到的 class 全列出来，逐个确认有样式：

```powershell
# 在 landing/ 目录下执行，列出 script 段里出现的所有 class
$t = Get-Content index.html -Raw
$js = $t.Substring($t.IndexOf('<script>'), $t.IndexOf('</script>') - $t.IndexOf('<script>'))
[regex]::Matches($js, "class=\\?""([^""\\]+)") | ForEach-Object { $_.Groups[1].Value -split '\s+' } | Sort-Object -Unique
```

当前需要覆盖的清单：`release` `is-latest` `release-head` `chev` `tag` `latest-badge` `rl-name` `rl-right` `rl-date` `release-body` `release-actions` `asset-meta` `dl-meta` `btn-primary` `btn-sm` `pill` `live-dot` `tl-dot` `tl-ver` `tl-date`。

**参考了 multica.ai 的排版节奏（大字号、克制配色、无边框卡片），未使用其任何品牌元素、字体、配色或图形。**

---

## 2. 已经上线了（GitHub Pages）

**当前线上地址：<https://wbsjl321.github.io/ai-image-workbench/>**

用的是 `wbsjl321/ai-image-workbench`（那个本就公开、只放 Release 的仓库）的 `gh-pages` 分支，
Pages 已经建好，HTTPS 强制开启（`https_enforced: true`），构建状态 `built`。

### 怎么更新线上页面

线上内容 = `gh-pages` 分支的根目录。改了 `landing/` 里的东西要重新推一次：

```powershell
# 1. 同步到部署工作副本
Copy-Item landing\index.html   _deploy-pages\index.html   -Force
Copy-Item landing\hero-bg.jpg  _deploy-pages\hero-bg.jpg  -Force
Copy-Item landing\app-icon.png _deploy-pages\app-icon.png -Force
Copy-Item landing\shots\ui-*.png _deploy-pages\shots\ -Force

# 2. 提交并推
cd _deploy-pages
git add -A
git commit -m "update landing"
git push https://github.com/wbsjl321/ai-image-workbench.git gh-pages:gh-pages
```

推完等 **30～60 秒** Pages 会重新构建（实测约 40 秒）。`git push` 返回 0 不代表已生效，
要回来看一眼线上返回的 HTML 里有没有你刚改的东西。

### 部署工作副本

`_deploy-pages/` 是从 `landing/` 挑出「必须上传」的四样重建的干净目录，
**不含** `_preview-*.png` / `_ref-multica-*.png` / `_real-bodies.json` / `_schemes/` / `*.bak-*`。

它只是一个 git 工作副本，**随时可以删**（需要时按上面的命令重建）。真正的线上内容在 GitHub 上。

### 已知限制：国内访问

`*.github.io` 在国内**不稳定**，部分地区/运营商可能打不开或很慢。
如果主要受众在国内、且要当成正式发布页，需要换成自有域名 + 国内服务器（要 ICP 备案）。
换域名的做法见下面「3. 按服务器环境分别怎么放」。

### 如果想换成自己的域名

有两种：

1. **继续用 Pages，只把域名指过来**（不用服务器，但 github.io 的线路问题依旧）：
   在 `gh-pages` 分支根目录加一个 `CNAME` 文件，内容就是域名（如 `dl.example.com`），
   然后去域名 DNS 加一条 `CNAME dl → wbsjl321.github.io`。加完回 Pages 设置里勾 Enforce HTTPS。
2. **搬到国内服务器**：见第 3 节，先办备案。

---

## 2.1 快速部署（自有服务器，通用）

把上表「必须上传」的四样复制到服务器的网站根目录即可：

```bash
# 本地 → 服务器（把路径换成你自己的）
scp -r landing/index.html landing/app-icon.png landing/hero-bg.jpg landing/shots \
  user@your-server:/var/www/yulinlin/
```

放好后浏览器访问 `https://你的域名/` 就应该看到页面。

**没有任何构建步骤**：不要运行 `npm install` / `npm run build`，也不要找 `dist/` 目录 —— 源文件就是产物。

---

## 3. 按服务器环境分别怎么放

### 3.1 Nginx

把文件放到站点根目录（如 `/var/www/yulinlin/`），然后：

```nginx
server {
    listen 80;
    server_name your-domain.com;

    root /var/www/yulinlin;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }
}
```

改完执行 `nginx -t && systemctl reload nginx`。

> `try_files ... /index.html` 让未命中的路径也回落到首页，避免用户手打路径时 404。

### 3.2 Apache

把文件放到 `DocumentRoot`（如 `/var/www/html/`）。如果目录里没有 `.htaccess`，Apache 默认就会返回 `index.html`，通常**无需额外配置**。

若使用了 `mod_rewrite` 且需要回落：

```apache
<IfModule mod_rewrite.c>
    RewriteEngine On
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteRule ^ /index.html [L]
</IfModule>
```

### 3.3 宝塔面板（最常见）

1. 「网站」→「添加站点」→ 填域名，PHP 版本选**纯静态**。
2. 进站点根目录（一般是 `/www/wwwroot/你的域名/`），删掉默认的 `index.html` / `404.html`。
3. 上传 `index.html`、`app-icon.png`，**并把 `shots` 整个目录一起传上去**（面板「文件」→ 上传；目录要先建好）。
4. 「网站」→「设置」→「SSL」→ 申请 Let's Encrypt 证书并打开「强制 HTTPS」。

宝塔不需要改伪静态规则，默认配置即可正常访问。

### 3.4 本地/内网快速看一眼

不想部署时，**直接双击 `index.html`** 就能在浏览器里打开（`file://` 协议）。
此时页面仍会尝试联网读取 GitHub 版本信息；若无网络，会自动回退到内置快照数据，不会白屏。
注意 `file://` 下同样需要 `app-icon.png` 和 `shots/` 与 `index.html` 保持相对位置，否则图标和图片会缺失。

---

## 4. 版本号是怎么自动更新的（原理）

页面**不写死版本号**，而是运行时向 GitHub 的公开 API 请求：

- 仓库：`wbsjl321/ai-image-workbench`
- 接口：`https://api.github.com/repos/wbsjl321/ai-image-workbench/releases?per_page=100`
  （定义在 `index.html` 的 `<script>` 顶部 `REPO` / `API_LIST` 变量）

流程：

1. **先用内置快照立刻渲染页面**，保证任何情况下都不出现空白（`SNAPSHOT` / `SNAPSHOT_LIST`）。
2. 同时在后台请求 GitHub Releases API（超时 9 秒）。
3. 请求成功 → 用真实数据覆写：最新版号、下载按钮链接、各版本日期/体积/更新日志。状态标记为 **「实时数据」**。
4. 请求失败 → 保留快照内容继续展示，状态标记为 **「缓存快照」**，并提示失败原因。

因此：**只要你在 GitHub 上发布新 Release，刷新页面就会自动显示新版本，不需要改代码。**
唯一例外是离线场景下的历史列表/体积等细节，会用快照兜底。

### 发新版本时，你到底要不要动这个页面？

**不用改代码。** 下面这些全部在页面打开时现拉，跟着新 Release 自动变：

| 位置 | 元素 id | 数据来源 |
| --- | --- | --- |
| 顶栏「下载」按钮链接 | `#nav-dl` | 最新 Release 的 `.exe` 资产 |
| 首屏主按钮链接 + 文案 | `#primary-download` / `#primary-download-label` | 同上 |
| 首屏「当前版本 vX.Y.Z」 | `#ver-pill-text` | 最新 Release 的 `tag_name` |
| 首屏「安装包约 N MB」 | `#hero-size` | 最新 `.exe` 资产的 `size` |
| FAQ「安装包有多大」里的数字 | `#faq-size` | **同上**（同一个 `asset.size`，两处不会打架） |
| 页脚版本号 | `#foot-version` | 最新 `tag_name` |
| 更新日志 11 条 | `#release-list` | Releases API 列表 |
| 版本时间线 | `#tl-items` | 各版本的 `published_at` |

**唯一要你留意的**：这个页面默认只显示**最新版**的下载按钮，旧版只留「在 GitHub 查看 →」。
这是 `renderReleases()` 里按列表下标 `i === 0` 判的，不是写死名单，所以发新版后新版会自动成为唯一可下载项。

> 历史坑（已修）：
> 1. `#faq-size` 以前是写死的 `78 MB`，JS 不管它 —— 发新版后首屏会显示新体积、FAQ 还写着旧的，两处对不上。现已改成与 `#hero-size` 同源。
> 2. `#nav-dl` 曾经从头到尾没有被赋值，`href` 一直是 `"#"`，顶栏那个最显眼的「下载」点了没反应。现已并入 `setPrimaryDownload()` 一起更新。

### 什么时候必须手动改 `index.html`

只有一种情况：**想让「离线快照」也跟上新版本**。

快照（`<script>` 里的 `SNAPSHOT` / `SNAPSHOT_LIST` / `SNAPSHOT_SIZES`）是 GitHub API 被限流或断网时的兜底数据，
**它不会自动更新**。不更新也不影响正常访问（有网时永远走实时数据），只是限流时用户会看到旧版本号。

想更新的话，把最新 Release 的 `tag_name` / `name` / 资产名 / 资产 URL / 资产字节数 / 更新说明正文
替换进这三个变量即可（都在 `<script>` 顶部，紧挨着 `REPO` 变量）。不改也完全能用。

### GitHub API 未认证限流（403）时的表现

GitHub 对**未登录**的 API 请求按来源 IP 限流（通常每小时 60 次）。用超后接口返回 `403`。

页面对此的处理是：

- 版本区状态标记显示 **「缓存快照」**；
- 历史版本列表与各版本更新说明仍完整显示（用内置快照）；
- 页面出现一行提示，说明接口暂时不可用，并给出「去 GitHub 看最新」链接；
- 下载按钮**依然可用** —— 它指向固定的 Releases 地址，不依赖 API。

也就是说：**限流只影响"版本号是否实时"，不影响用户下载。** 页面永远不会白屏或报错崩溃。

### 只提供最新版下载（重要）

版本列表里**只有最新版（列表第一项）有下载按钮**，其余 10 个旧版本只显示版本号、日期、更新说明，
并保留「在 GitHub 查看 →」链接（万一用户确实需要旧版，可自行前往 GitHub Releases 页面下载）。

这个规则同时作用于**实时数据**与**离线快照**两条链路（由 `renderReleases()` 里的 `isLatest` 判断，
不是写死的名单），所以将来发布新版本时无需改代码，新版本会自动成为唯一可下载项。

如果你想改回「每个版本都能下载」，把 `renderReleases()` 里那段三元判断改成无条件渲染
`.btn-primary` 按钮即可（搜 `isLatest` 或 `下载此版本`）。

### 版本时间线

版本区顶部有一条横向时间线（`id="version-timeline"`），节点由 `renderTimeline()` 从真实的
`publishedAt` 生成，最新版节点高亮。窄屏（≤760px）自动转纵向排列。

### 应用图标

顶栏品牌图标用的是**应用真实图标**，来自源码仓库的 `electron/icon.png`，
已复制为 `app-icon.png`（51 KB，1024×1024），同时用作 favicon。
**部署时必须一起上传**，否则顶栏图标会显示成空块。

### 想让它始终实时？两个可选办法

- **办法一（推荐，零成本）**：不处理。限流只是偶发，且页面有缓存兜底。
- **办法二**：自建一个极小的转发接口（如 Cloudflare Worker / 服务端脚本），在服务端带 Token 请求 GitHub API 后转发给页面，再把 `index.html` 里的 `API_LIST` 换掉。
- 不建议把 GitHub Token 写进前端 `index.html` —— 那是公开的，Token 会泄露。

---

## 5. 怎么改文案

全部文案都在 `index.html` 里，直接改文字即可，**不需要碰 CSS / JS 就能完成 90% 的修改**。

常用位置速查（行号会随编辑漂移，**按关键词搜索更稳**）：

| 想改什么 | 怎么找 |
| --- | --- |
| 浏览器标签标题 | 搜 `<title>` |
| 分享/搜索描述 | 搜 `name="description"` |
| 配色 / 圆角 / 间距 / 字号层级 | 搜 `:root` 段落，或直接搜 `--accent` / `--paper` / `--r-btn` |
| 顶部导航品牌名与菜单 | 搜 `class="logo"` / `class="hbar"` |
| 首屏大标题 | 搜 `<h1>`（在 `class="hero"` 段内） |
| 首屏说明段 | 搜 `class="lede"` |
| 首屏右侧窗口截图 | 搜 `class="shotwin"`（对应 `shots/ui-gen.png`） |
| 窗口标题栏文字 | 搜 `class="t"`（在每个 `class="bar"` 内） |
| 下载按钮文字 | 搜 `primary-download-label` |
| 「怎么用」四步 | 搜 `id="how"` |
| 界面截图墙标题与图注 | 搜 `id="ui"` |
| 功能卡片文案 | 搜 `id="specs"` |
| 版本与更新日志区 | 搜 `id="log"` |
| 版本时间线 | 搜 `id="version-timeline"` 与 `renderTimeline()` |
| 版本列表的下载规则 | 搜 `isLatest`（在 `renderReleases()` 内） |
| 应用图标 / favicon | 搜 `app-icon.png` |
| 常见问题 FAQ | 搜 `id="q"` |
| 联系方式 | 搜 `id="contact"` |
| 页脚 | 搜 `<footer>` |
| 离线兜底的版本数据 | 搜 `SNAPSHOT_LIST` |
| 各版本更新说明正文 | 搜 `SNAPSHOT_BODIES`，改对应 tag 的字符串 |

> 区块 id 在「深色工作室」改版时统一换过：`start→how`、`screenshots→ui`、`features→specs`、`releases→log`、`faq→q`。若你手上有旧链接，按这张表换。

**改文案时不要动的地方**（容易改坏）：

- `id="..."` 属性值（如 `id="primary-download"`、`id="ver-pill"`、`id="tl-items"`）—— JS 靠这些 id 找元素，改了功能就失效。
- 形如 `https://github.com/wbsjl321/ai-image-workbench/releases/download/v1.1.5/...` 的下载链接。
- `<script>` 里的变量名与请求地址。

改完**保存文件、刷新浏览器**即可，没有编译过程。

---

## 6. 上线前还要你定的几件事

页面本身已经能直接上线，剩下这些是**只有你能拍板**的：

- [ ] **站点域名 / 备案** —— 需要页脚展示备案号或版权声明的话，自己加一段。
- [ ] **联系渠道** —— 现在留的是 GitHub 仓库、Issues、`api.wbsjl.com` 三个真实地址。如果还有微信群 / QQ 群 / 邮箱，加进 `id="contact"` 区；没有就保持现状。
- [ ] **首屏背景图** —— 现在是 `shots/hero-bg.jpg`。想换的话，替换成同名文件即可，CSS 不用动。
- [ ] **产品正式名称** —— 页面按源码 `electron-builder.yml` 的 `productName` 用的是「雨霖霖」；`ai-image-workbench` 只作为仓库名/技术标识保留。若对外品牌另有其名，全局替换。
- [x] **更新日志正文** —— 已把 11 个版本的真实更新说明从 GitHub Releases 导出，内置为 `index.html` 里的 `SNAPSHOT_BODIES`。即使 GitHub API 被 403 限流回退快照，每个版本的说明也都能显示。
- [x] **产品截图** —— 已用 6 张真实界面截图替换掉原来的 CSS 占位框（见 `shots/ui-*.png`）。
- [x] **功能介绍与 FAQ** —— 已按真实功能和实机截图重写，全文无占位标记。

发新版后想同步离线快照的更新说明：

```bash
gh api repos/wbsjl321/ai-image-workbench/releases/latest \
  --jq '.tag_name + " => " + (.body | @json)'
```

把输出里的 `"<tag>" => "<json body>"` 追加进 `index.html` 的 `SNAPSHOT_BODIES` 即可。

---

## 7. 数据一致性备忘（供后续维护参考）

- 当前最新版 **v1.1.5**，资产 `ai-image-workbench-1.1.5-setup.exe`，约 **78.4 MB**。
- 历史上共 **11 个版本**：v1.1.5 / v1.1.4 / v1.1.3 / v1.1.2 / v1.1.1 / v1.1.0 / v1.0.6 / v1.0.4 / v1.0.3 / v1.0.2 / v1.0.1。
- 体积规律：v1.1.5~v1.1.2 约 78 MB，v1.1.1 约 126 MB，v1.1.0 约 77 MB，
  v1.0.6 约 78 MB，v1.0.4 约 81 MB，v1.0.3/v1.0.2/v1.0.1 约 154 MB。
- 复核命令（需本机已装并登录 `gh`）：

  ```bash
  gh api repos/wbsjl321/ai-image-workbench/releases \
    --jq '[.[] | {tag:.tag_name, date:.published_at, assets:[.assets[]|{name,size,download_count}]}]'
  ```

页面联网时会自动取到上述信息，**这个清单只是离线兜底快照的对照表**，发新版后无需手动更新。
