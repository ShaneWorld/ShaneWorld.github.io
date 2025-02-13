---
title: 用 Hugo 搭建个人博客
description: hugo-theme-dream 建站过程
date: 2025-01-28T05:55:07+08:00
lastmod: 2025-01-28T05:55:07+08:00
author: Shane
avatar: /me/shane.png
categories:
  - Blog
tags:
  - hugo
draft: false
---

## 引言

在之前使用 jekyll 作为个人博客的时候，博客的构建过程仍然过于繁琐，且稳定性堪忧，比如上次在本地运行时报错，始终无法解决。

因此，我又重回 hugo，在稳定性、易用性、构建速度上都有了提高。这篇文章记录一下在 **Arch Linux** 中的建站过程。

## 安装 Hugo

Hugo 的准备步骤很简单，只需要安装 Hugo 工具即可：

```bash
sudo pacman -S hugo
```

要确保安装的 Hugo 是 extended 版本：

```bash
~ ❱ hugo version
hugo v0.142.0+extended+withdeploy linux/amd64 BuildDate=unknown
```

输出应包含 `+extended+withdeploy` 字段。

## 创建博客

生成一个名为 `Blog` 的文件夹，存放博客内容。执行：

```bash
hugo new site Blog
```

在博客根目录中初始化 git 仓库：

```bash
git init
```

## 博客配置

我使用的主题是自己修改过的 [hugo-theme-dream](https://github.com/shaneworld/hugo-theme-dream)，修复了[原主题](https://github.com/g1eny0ung/hugo-theme-dream)的以下 bug 并做了一些定制：

- 修复了 dropdown menu 展开后无法收回的问题
- 修复了鼠标点选位置和目录栏目的偏移问题
- 删除了上、下篇文章切换按钮的 `上一页`、`下一页` 文字，更协调统一

我采用的是将主题作为 git 子模块添加的方式，在博客根目录中：

```bash
git submodule add git@github.com:shaneworld/hugo-theme-dream.git themes/dream
```

然后修改 `hugo.toml` 文件，具体的配置参考原主题 [官方文档](https://hugo-theme-dream.g1en.site/)或者根据 [我的配置文件](https://github.com/shaneworld/shaneworld.github.io/blob/master/hugo.toml)进行修改。

`contents` 目录应遵循如下结构：

```bash
  content
 ├──  about
 │   ├──  index.md
 │   └──  me.md
 ├──  posts
 │   ├──  _index.md
 │   ├──  blog-with-chirpy
 │   ├──  blog-with-hugo
 │   └──  c-sharp-begin
 └──  search
     └──  _index.md
```

其中 `index.md` 和 `_index.md` 文件必须存在，内容参考 [我的博客](https://github.com/shaneworld/shaneworld.github.io) 自行修改。

## 本地运行

在项目根目录中，执行：

```bash
hugo server -D
```

也可以使用 `hugo server`，但其仅渲染 `draft: false` 的文章，且无法实时监听文章变化。

执行后默认在 `localhost:1313` 端口可访问博客。在根目录中会生成 `public` 文件夹存放生成的静态文件。

## 部署

新建一个 github 仓库，命名必须为 `USERNAME.github.io`，`USERNAME` 为 github 用户名。在设置里将 pages 里的 `Build and deployment` Source 改为 `GitHub Actions`。

然后在博客根目录中新建 `.github/workflows/hugo.toml`，写入以下内容：

```yaml
name: Deploy Hugo site to Pages

on:
  # Runs on pushes targeting the default branch
  push:
    branches:
      - master

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# Sets permissions of the GITHUB_TOKEN to allow deployment to GitHub Pages
permissions:
  contents: read
  pages: write
  id-token: write

# Allow only one concurrent deployment, skipping runs queued between the run in-progress and latest queued.
# However, do NOT cancel in-progress runs as we want to allow these production deployments to complete.
concurrency:
  group: "pages"
  cancel-in-progress: false

# Default to bash
defaults:
  run:
    shell: bash

jobs:
  # Build job
  build:
    runs-on: ubuntu-latest
    env:
      HUGO_VERSION: 0.141.0
    steps:
      - name: Install Hugo CLI
        run: |
          wget -O ${{ runner.temp }}/hugo.deb https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb \
          && sudo dpkg -i ${{ runner.temp }}/hugo.deb
      - name: Install Dart Sass
        run: sudo snap install dart-sass
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: recursive
          fetch-depth: 0
      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v5
      - name: Install Node.js dependencies
        run: "[[ -f package-lock.json || -f npm-shrinkwrap.json ]] && npm ci || true"
      - name: Build with Hugo
        env:
          HUGO_CACHEDIR: ${{ runner.temp }}/hugo_cache
          HUGO_ENVIRONMENT: production
          TZ: America/Los_Angeles
        run: |
          hugo \
            --gc \
            --minify \
            --baseURL "${{ steps.pages.outputs.base_url }}/"
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public

  # Deployment job
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

> [!TIP]
> branches 要和你创建的分支对应上。

（可选）添加 `.gitignore` 文件，排除如下文件：

```bash
public
.hugo_build.lock
```

然后将博客根目录推送到 github 刚才新建的远端仓库。

```bash
git add .
git commit -m "first commit"
git branch -M master
git remote add origin xxxxxxxxxxxx
git push -u origin master
```

上面的提交信息和远端仓库自行填写。

推送成功后 github 会自动部署，完成后即可在 `USERNAME.github.io` 查看你的博客了。

## 更新博客

```bash
hugo new posts/BLOGNAME/index.md
```

完成后将 front-matter 的 `draft` 属性修改为 `false`，然后将更改推送到远端仓库即可，github 会自动部署变更。
