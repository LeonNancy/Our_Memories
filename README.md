# 我们的旅行与日常

记录一起去过的地方，以及平凡又珍贵的日子。

这是一个 Hugo 相册网站，目标地址为 **https://ljkyzr.com/**。首页展示相册封面，进入相册后展示照片列表，点击照片可查看大图和逐张配文。页面使用中文和浅色主题，大图浏览保持主题原有的深色背景。

## 项目结构

```text
our_memory/
├── hugo.toml                     # 网站配置、自定义域名、语言
├── .hugo-version                 # 本地与 CI 统一：0.145.0 Extended
├── .github/workflows/hugo.yaml   # main 推送 / 手动触发 Pages 部署
├── content/
│   ├── _index.md                 # 首页标题与介绍
│   ├── travel/                   # 示例：旅行记忆
│   │   ├── index.md              # 相册名称、日期、简介、封面、逐张配文
│   │   └── example-*.jpg
│   └── daily/                    # 示例：日常碎片，结构相同
├── assets/css/custom.css         # 中文字体、长配文滚动与安全边距
├── assets/js/lightbox.js         # 根目录覆盖：配文与触摸滚动
├── layouts/partials/title.html  # 根目录覆盖：显示相册日期
└── themes/gallery/              # 完整下载版主题 4.9.3，含 MIT LICENSE
```

网站内容请写在根目录的 `content/` 中。编辑器原先打开的 `themes/gallery/exampleSite/content/featured-album/index.md` 是主题演示文件，修改它不会改变本站。保留整个主题目录；无需 Git submodule、Hugo Module、Go、Node.js 或 npm。

## 安装与本地预览

本项目固定使用 **Hugo Extended 0.145.0**（主题要求至少 0.123.0 Extended），以兼容该版主题的模板和内置 LibSass。`.hugo-version` 是 CI 读取的版本源；升级前先验证主题兼容性，不要仅将 CI 改成 latest。当前较新的 Hugo 已改变 Sass 依赖，不能假定任意版本都可直接替换。

从 [Hugo 官方 v0.145.0 Releases](https://github.com/gohugoio/hugo/releases/tag/v0.145.0) 下载与你的操作系统对应的 **extended** 压缩包：macOS 使用 `hugo_extended_0.145.0_darwin-universal.tar.gz`，Linux x64 使用 `hugo_extended_0.145.0_linux-amd64.tar.gz`，Windows x64 使用相应的 `windows-amd64.zip`。可用同页 `hugo_0.145.0_checksums.txt` 核对 SHA-256。

解压后将 `hugo`（Windows 为 `hugo.exe`）放到项目 `.tools/hugo/`，该目录已忽略。也可以自行放入 PATH；以下命令均在项目根目录运行：

```sh
.tools/hugo/hugo version
# 应显示 v0.145.0 和 +extended
.tools/hugo/hugo server --bind 127.0.0.1 --baseURL http://localhost:1313/
```

浏览器访问 `http://localhost:1313/`，按 Ctrl+C 停止。Windows PowerShell 使用 `.\.tools\hugo\hugo.exe` 替换命令路径；若已经加入 PATH，直接使用 `hugo`。

生产构建（与 CI 相同参数）：

```sh
.tools/hugo/hugo --gc --minify --panicOnWarning
```

生成目录为 `public/`，不提交。原图越多，首次图片处理越久；后续构建会复用本地缓存。源图建议使用 JPG 或 PNG，本版主题 README 提醒避免使用 WebP 源图。手机 HEIC 请先转换为 JPG。网站运行时不依赖外部图片或字体服务。

## 添加相册、封面和逐张配文

每个相册是一个文件夹，内含 **`index.md`** 与照片。例如新建 `content/my-album/`，放入 `01.jpg`、`02.jpg`，再创建以下 `index.md`：

```yaml
---
title: 这里填写相册名称
date: 2026-01-01 # 示例日期，请改为实际日期；未来日期默认不发布
description: 这里填写相册简介。
resources:
  - src: 01.jpg
    title: |-
      这里是第一张照片的配文。
      第二行会保留换行。

      空行可以分隔段落。
    params:
      cover: true
  - src: 02.jpg
    title: |-
      这里是第二张照片的配文。
      与第一张分别保存、分别显示。
---
```

`src` 必须与实际文件名一致，大小写也要一致（GitHub 的 Linux 构建区分大小写）。使用空格缩进，不要用 Tab。`title: |-` 中的 `|` 保留换行，`-` 去掉末尾多余空行；不要改成会折叠换行的 `>`。配文是纯文本，直接写中文，不需要 HTML 或 Markdown 换行标签。

本版主题读取 **`resources[].title`**，缺省时可能回退到照片 EXIF 的 `ImageDescription`，而不是 `caption`、`alt` 等其他主题的字段。本站根目录脚本读取主题产生的文字并进行 HTML 转义，用主题自带的 PhotoSwipe Dynamic Caption 显示；CSS 保留换行。配文固定在下方，最长占视窗 30%，超长文字在区域内滚动；顶部关闭、中央切换按钮有独立空间。可以用键盘 Tab 聚焦配文区域进行滚动，用 Esc 关闭大图。

每个相册只给一张照片设置 `params.cover: true`；否则主题会选文件名含 `feature` 的图片，再回退到第一张。若封面不想出现在照片列表，可在该资源的 `params` 中再加 `hidden: true`，同时至少保留一张可见照片。没有可见图片的相册不会出现在首页。

相册默认按日期由新到旧排列，可用 `weight` 调整。照片默认按文件名排序，建议命名 `01.jpg`、`02.jpg`。若需手动排序，在相册中设置 `params.sort_by: Params.weight`，并分别设置每张图片的 `params.weight`（如 10、20）。

`index.md` 最后的 `---` 下方可追加相册正文，显示在照片列表后；首页介绍则在 `content/_index.md` 的 `description`，同时可更新 `hugo.toml` 的站点描述。

## 删除示例与替换照片

目前没有真实照片，`content/travel/` 和 `content/daily/` 各有 3 张本地程序绘制的几何占位 JPG，图内都写有“示例占位图 · 非真实照片”。相册日期、简介、配文均为示例，不代表真实经历。

- **直接替换：** 将自己的照片复制到相册文件夹，修改该相册 `index.md` 的 `resources`，逐一匹配新文件名、配文和封面。删除旧的 `example-*.jpg` 以及对应资源条目，更新日期和简介中的“示例”说明。
- **重新开始：** 删除 `content/travel/` 和 `content/daily/` 两个示例文件夹，保留 `content/_index.md`，按上一节建立自己的相册。至少添加一个有照片的相册后，首页才会显示封面。
- 原图文件只要还在相册文件夹中，即使删除了 `resources` 条目，也仍可能出现在照片列表，因此删除示例要同时删除图片文件。

删除或改名后，若本地 `public/` 残留旧文件，可重新构建到一个空目录。GitHub Actions 每次使用干净检出，不会把上次发布的旧图片带回去。

默认行为与原主题一致：提供原图下载。原文件可能包含 EXIF，`disableLatLong` 只影响 Hugo 提取数据，不会擦除下载原图的元数据；如不希望公开位置，请在放入项目之前处理好源图。主题的 `private: true` 仅隐藏列表/搜索索引，不是访问控制。

## 上传到自己的 GitHub 仓库

本次仅修改本地文件，没有初始化远程、推送或发布。请自己在 GitHub 创建一个空仓库（名字任意，例如 `our_memory`），再在项目根目录执行。下面的 `<YOUR_GITHUB_USERNAME>` 和 `<YOUR_REPOSITORY>` 必须替换：

```sh
git init -b main
git add .
git status
git commit -m "Set up our Hugo photo gallery"
git remote add origin https://github.com/<YOUR_GITHUB_USERNAME>/<YOUR_REPOSITORY>.git
git push -u origin main
```

这些初始化命令适用于当前尚未初始化 Git 的文件夹；已有仓库请沿用现有历史和 remote。登录 GitHub 使用 GitHub Desktop、SSH 或个人访问令牌等受支持方式，不要把凭据写入项目。也可用 GitHub Desktop 上传，须包含隐藏的 `.github/workflows/hugo.yaml` 与 `.hugo-version`。

提交前确认包含 `themes/gallery/`（含许可证）、`content/`（含照片）、`assets/`、`layouts/`、配置及工作流；不要提交 `.tools/`、`public/` 和缓存。主题依旧是普通文件夹，没有 `.gitmodules` 或根目录 Hugo Module 导入。

## GitHub Pages 与 Actions

1. 进入仓库 **Settings → Pages → Build and deployment → Source**，选择 **GitHub Actions**。不要另行生成一套工作流，项目已自带 `.github/workflows/hugo.yaml`。
2. 确保仓库允许 GitHub Actions 运行这些官方 actions；如果 `github-pages` 环境设置了分支限制，允许 `main`。
3. 在 **Settings → Pages → Custom domain** 填入 **`ljkyzr.com`**（不带 `https://` 或路径）并保存，再按下节配置 DNS。
4. 初次推送若发生在启用 Pages 之前，构建可能在 Configure Pages 处失败。完成设置后，在 **Actions → Build and deploy Hugo to GitHub Pages → Run workflow** 选择 `main` 重跑。此后推送 `main` 会自动构建和部署。
5. DNS 校验和证书签发完成后勾选 **Enforce HTTPS**。此选项可能需要最长约 24 小时才可用。访问 `https://ljkyzr.com/`，检查首页、相册与大图。

工作流使用 `.hugo-version` 的 Extended 发行包及官方 SHA-256 校验，运行 `hugo --gc --minify --panicOnWarning`，上传 `public/` 后部署。`pages: write` 与 `id-token: write` 仅授予部署任务，构建只读源码和 Pages 配置（私有仓库也需要 `pages: read`）；并发组为 `pages`，不取消正在进行的部署。无需个人 token、`gh-pages` 分支、submodule 或额外下载主题。

已核对的官方动作版本为 [`checkout@v6`](https://github.com/actions/checkout/tree/v6)、[`configure-pages@v5`](https://github.com/actions/configure-pages/tree/v5)、[`upload-pages-artifact@v4`](https://github.com/actions/upload-pages-artifact/tree/v4)、[`deploy-pages@v4`](https://github.com/actions/deploy-pages/tree/v4)。构建产物上传、部署环境及权限按照 [GitHub 自定义 Pages 工作流文档](https://docs.github.com/en/pages/getting-started-with-github-pages/using-custom-workflows-with-github-pages) 配置。

**本项目不需要 `CNAME` 文件。** 官方文档说明：自定义 Actions 工作流发布时不会创建该文件，已有文件也会被忽略。域名绑定在 Pages 设置中完成，DNS 的 `CNAME` 记录与仓库里的同名文件是两回事。构建直接采用 `hugo.toml` 的 `https://ljkyzr.com/`，不会使用 `configure-pages` 返回的旧 `github.io/仓库名/` 路径覆盖它；首次使用仓库默认子路径地址可能不完整，应以配置完成的自定义域名验证。[依据：GitHub 自定义域名文档](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site)

## 阿里云 DNS

进入阿里云 **云解析 DNS → 公网权威解析 → ljkyzr.com → 解析设置 → 添加记录**（界面名称以控制台为准），解析线路和 TTL 可用默认值。需确认域名确实使用此处的权威 DNS；否则在当前权威 DNS 服务商修改。阿里云的 `@` 表示主域名。[阿里云添加解析记录说明](https://help.aliyun.com/zh/dns/pubz-add-parsing-record)

先在 GitHub Pages 保存 Custom domain，再设置下列记录。每个 IP 单独一条：

| 记录类型 | 主机记录 | 记录值 |
| --- | --- | --- |
| A | @ | 185.199.108.153 |
| A | @ | 185.199.109.153 |
| A | @ | 185.199.110.153 |
| A | @ | 185.199.111.153 |
| CNAME | www | `<YOUR_GITHUB_USERNAME>.github.io` |

将 `<YOUR_GITHUB_USERNAME>` 换成自己的 GitHub 用户名（若用组织仓库则换组织名）。`www` 的值不带协议、不带仓库名、不带路径；不要使用 `*.pages.github.io` 的私有站点地址。正确配置后 `www.ljkyzr.com` 会跳转到 Pages 中设置的主域名 `ljkyzr.com`。以上地址来自 [GitHub 官方域名文档](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site)。

可选 IPv6：保留上述 A 记录，再加以下四条 AAAA；不需要 IPv6 可以不加。

| 记录类型 | 主机记录 | 记录值 |
| --- | --- | --- |
| AAAA | @ | 2606:50c0:8000::153 |
| AAAA | @ | 2606:50c0:8001::153 |
| AAAA | @ | 2606:50c0:8002::153 |
| AAAA | @ | 2606:50c0:8003::153 |

仅清理 `@` 和 `www` 上冲突的旧网站解析，不要误删邮箱 MX、TXT 等其他用途记录。不要添加通配符 `*` 解析。DNS 生效和 HTTPS 证书签发需要时间，可用 `dig ljkyzr.com A +short`、`dig www.ljkyzr.com CNAME +short` 查看。记录与时效说明均按 [GitHub 官方文档](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site) 核对。

## 私有仓库与网站可见性

GitHub Free 支持公开仓库的 Pages；个人私有仓库需要 GitHub Pro，组织私有仓库通常需要 GitHub Team 或 Enterprise Cloud 等支持套餐。GitHub 官方也列出 Enterprise Server 支持，具体取决于对应企业服务。[GitHub Pages 套餐说明](https://docs.github.com/en/pages/getting-started-with-github-pages/what-is-github-pages)

**仓库私有不等于网站私有。** 普通 Pages 发布的网页与照片仍然可被访问；组织使用 Enterprise Cloud 才有官方私有 Pages 访问控制能力，且需单独设置。本项目没有登录系统。相册用于私人回忆时，请自行决定哪些照片适合公开。[GitHub Pages 站点可见性文档](https://docs.github.com/en/enterprise-cloud@latest/pages/getting-started-with-github-pages/changing-the-visibility-of-your-github-pages-site)

官方套餐、DNS 与 Actions 信息核对日期：2026-11-16。主题源码和 MIT 许可原样保留在 `themes/gallery/`，根目录仅做站点配置及少量覆盖。

## 本次验证记录

- 在 macOS arm64 上下载并通过官方 SHA-256 校验的 Hugo Extended 0.145.0 已实际构建成功，启用了 `--gc --minify --panicOnWarning`；本地缓存指定在项目内，CI 使用 runner 临时缓存。
- 生成了两个相册、6 张源图及缩略图/大图；检查的 51 个本地链接均有对应文件。页面为中文与浅色，canonical 使用 `https://ljkyzr.com/`，没有旧仓库子路径。
- 压缩后的 6 份图片配文与源文件逐一完全匹配，包含中文、换行和空行；自定义滚动样式与脚本已进入编译产物。
- TOML、YAML、工作流结构和图片文件名检查通过；Git 提交范围模拟确认主题、许可证、照片与工作流不会被忽略，主题文件逐文件校验未改动。
- 当前环境的浏览器控制接口未启用，且无现成自动化浏览器，因此没有完成桌面/手机实际视觉检查与触摸滚动验收。GitHub Actions 线上运行、DNS 和 HTTPS 也尚未验证，需在你自行上传和配置后完成；本次没有推送或发布。
