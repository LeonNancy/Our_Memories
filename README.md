# 我们的旅行与日常 · Multiverse 多相册版

这是完整的静态 HTML 网站，不需要 Hugo、Node.js 或编译。基于 HTML5 UP Multiverse；保留 LICENSE.txt 和页面里的设计署名。

## 文件说明

| 文件 | 用途 |
| --- | --- |
| index.html | 首页：两个相册封面与入口 |
| japan.html | 日本旅行：6 张示例照片及配文 |
| daily.html | 日常碎片：6 张示例照片及配文 |
| assets/css/albums.css | 多相册新增样式、手机和配文适配 |
| assets/js/main.js | 原模板脚本；只在相册页启用大图浏览 |
| images/fulls/ | 大图 |
| images/thumbs/ | 缩略图 |
| CNAME | 自定义域名 ljkyzr.com |
| .nojekyll | 跳过 Jekyll 处理 |

目前所有照片来自原模板，配文为明确的示例，不代表你们的真实经历。发布前请替换。

## 本地预览

双击 index.html 即可查看，链接、图片和脚本均使用相对路径。
也可以在本目录运行 `python3 -m http.server 8000`，然后访问 http://localhost:8000 。

## 改照片和配文

打开 japan.html 或 daily.html。每个 `<article class="thumb">` 对应一张照片：

```html
<article class="thumb">
  <a href="images/fulls/my-photo.jpg" class="image" aria-label="查看我们的照片">
    <img src="images/thumbs/my-photo.jpg" alt="我们的照片描述" />
  </a>
  <h2>照片标题</h2>
  <p>写下这张照片的故事。<br />这里是新的一行。</p>
</article>
```

href 指向大图，img 的 src 指向缩略图。将图片复制到对应目录，确保文件名和大小写一致。
也可以让两者指向同一张图片，但为加载速度建议准备较小的缩略图。
h2 和 p 必须放在照片链接后面，原模板的大图脚本会读取它们。
显示小于号时写 `&lt;`，显示与号时写 `&amp;`。不要把富文本粘贴造成的 `\<`、`&#x9;` 写进 HTML 标签。

首页封面在 index.html 的 `.album-card` 里修改，替换 img 的 src。
新增/删除照片后，相册数量和文字计数是手动维护的，请同步修改首页和相册标题区。
替换全部示例后，可删除页面中的示例说明；保留 HTML5 UP 的设计署名。

## 新增相册

1. 复制 japan.html，改名如 birthday.html。
2. 修改页面 title、description、标题、简介和照片列表。
3. 在 index.html 复制一个完整的 `<article class="album-card">`，将 href 改为 birthday.html，修改封面、文字和计数。
4. 可同步更新各页底部“翻一翻我们的相册”的链接。

首页封面不要使用 `class="thumb"` 或 `class="image"`；相册页 `<main>` 保持 `class="photo-gallery"`。

## 上传 GitHub Pages（分支发布）

1. 先备份原 Hugo 项目，使用本包内容替换网站项目；不要把新文件嵌套放进旧 Hugo 的主题文件夹。
2. 上传本文件夹里面的内容，确保仓库根目录直接包含 index.html、japan.html、daily.html、assets、images、CNAME。
3. 停用/删除旧 Hugo 的 `.github/workflows/hugo.yaml` 或其他会部署同一个 Pages 网站的旧工作流，避免覆盖本网站。
4. GitHub 仓库 Settings → Pages → Source 选择 Deploy from a branch，分支选择 main，目录选择 /(root)，保存。
5. Custom domain 保持 ljkyzr.com。DNS 沿用已经配置好的记录，证书就绪后勾选 Enforce HTTPS。
6. 上传 .nojekyll（macOS Finder 按 Command+Shift+. 可显示隐藏文件）。这个版本不需要自定义构建 workflow，GitHub 仍会自动执行 Pages 发布任务。

仓库若为私有，GitHub Pages 仍需支持该功能的套餐；纯 HTML 不改变这一限制。普通个人 GitHub Pages 发布的网站公开可访问。

## 本次调整

- 新增相册封面首页、japan.html 与 daily.html 两个独立相册页。
- 沿用原版 jQuery / Poptrox 大图、前后切换、Esc 关闭和标题配文。
- 首页链接不会被当成大图链接拦截；顶部导航保留原生键盘链接行为。
- 增加中文字体回退、移动端相册排版和可滚动长配文。
- 删除无后端的联系表单和空社交链接，换为相册目录。
- 全部图片、脚本、样式及字体随包提供，无运行时 CDN 依赖。

## 验证记录

已检查 3 个 HTML 页面的 71 处本地链接/资源引用、CSS 资源路径、两张相册封面和 12 张照片的配文结构，以及 main.js 的语法；未发现缺失文件。当前执行环境限制浏览器启动，因此未完成浏览器中的交互与视觉验收。上传前请本地预览并检查大图效果。
