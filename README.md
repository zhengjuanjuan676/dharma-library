# 佛法共学文库

一个只读的 Hugo 静态文库：版块列表、文章阅读、标签/系列、搜索、RSS。
没有注册、登录、发帖、回复、评论、数据库和服务器。

## 技术栈

- **Hugo**：静态生成，Markdown 写作
- **Cloudflare Pages**：免费托管、全球 CDN、自动 HTTPS
- **Pagefind**：静态全文搜索
- **Cloudflare Web Analytics**：免费、无 Cookie 统计（可选）

## 本地运行

需要 Hugo（建议 0.140+）和 Node.js（Pagefind 用）。

```bash
hugo server -D
```

打开 `http://localhost:1313`。

搜索页的资源由 Pagefind 生成，`hugo server` 下不可用；需要完整构建后预览：

```bash
hugo --gc --minify
npx --yes pagefind --site public
npx --yes serve public
```

## 构建

```bash
hugo --gc --minify
npx --yes pagefind --site public
```

产物在 `public/`。

## 部署到 Cloudflare Pages

1. 把仓库推到 GitHub。
2. Cloudflare Dashboard → Workers & Pages → Create → Pages → Connect to Git。
3. 选择仓库，配置：

   - **Build command**：`hugo --gc --minify && npx --yes pagefind --site public`
   - **Build output directory**：`public`
   - **Environment variable**：`HUGO_VERSION` = 当前稳定版（例如 `0.140.0`，以 [Hugo Releases](https://github.com/gohugoio/hugo/releases) 为准）

4. 绑定自定义域名，Cloudflare 会自动签发证书。
5. 以后 `git push` 即发布；PR 会生成预览链接。

## 写作

新建文章：

```bash
hugo new content jinglun/wo-de-wenzhang.md
```

草稿默认 `draft: true`，本地 `hugo server -D` 可见，构建时不会发布。

### Front matter

```yaml
---
title: "文章标题"
date: 2026-11-01
lastmod: 2026-11-01
summary: "列表页摘要，一到两句。"
tags: ["般若", "心经"]
series: ["般若入门"]
author: "编辑部"
source: "《般若波罗蜜多心经》，CBETA, T08, no. 251。"
---
```

- `summary` 用于列表与分享卡片；不写则自动截取正文。
- `source` 显示在文末“参考”一行，支持 Markdown。
- `series` 会生成系列页；同一系列用同一个名字。
- 正文用 `##` / `###` 分节，自动生成右侧目录。

### 版块

`content/` 下每个目录是一个版块，`_index.md` 里的 `title`、`description`、`weight` 控制导航顺序和卡片文案。

## 目录结构

```
.
├── hugo.toml
├── content/
│   ├── rumen/          # 初学入门
│   ├── jinglun/        # 经论共学
│   ├── shixiu/         # 实修心得
│   ├── wenda/          # 问答存档
│   ├── ziyuan/         # 法宝资源
│   ├── guanyu/         # 关于本站
│   └── search.md
├── layouts/
│   ├── _default/       # baseof / list / single / terms / search / 404
│   ├── partials/       # head / header / footer / article-row / pagination
│   ├── index.html      # 首页
│   └── robots.txt
├── assets/css/main.css
└── static/             # favicon、_headers
```

## 上线前要改的地方

1. `hugo.toml` 的 `baseURL` 改成真实域名。
2. `hugo.toml` 的 `params.email` 改成真实信箱。
3. `content/guanyu/index.md` 里的联系邮箱。
4. `hugo.toml` 的 `title` / `params.description`。
5. 按需调整 `assets/css/main.css` 顶部的颜色变量。

## 设计说明

- 文库式列表：标题、摘要、更新日期、阅读时长、标签，没有回复框和楼层。
- 阅读优先：正文最大宽度 720px，18px 字号，1.95 行高，宋体正文 + 黑体界面。
- 深浅色：跟随系统，可手动切换，存在 `localStorage`。
- 无 JS 框架；只有主题切换和搜索两段脚本。
- 无 Cookie、无评论、无第三方追踪（除非你加统计）。

## 注意

- Pagefind 的中文分词取决于浏览器与索引；上线后请实测。若效果不好，可改用 FlexSearch，或先依赖标签/目录浏览。
- 本站是静态站，没有服务端；所有“互动”都通过来信完成。
- 文章中的 `{{< relref >}}` 会在构建时校验链接，路径写错会直接报错，这是好事。

## 成本

域名约 ￥86/年；Cloudflare Pages、Pagefind、HTTPS、CDN 均免费。
