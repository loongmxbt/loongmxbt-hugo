+++
date = "2015-03-04T11:21:04+08:00"
draft = true
title = "使用 Hugo 搭建 Github 静态博客"
tags = ["Hugo", "Go", "Github", "静态博客"]
categories = ["Web开发"]
+++

### Github 静态博客是啥

自从 Github 推出 Pages 服务以来，静态博客程序就层出不穷。所谓『静态博客』，就是所有页面均为 HTML 静态页面的博客。与常见的 Wordpress 等动态博客系统不同，它没有数据库，没有动态语言，没有复杂的后台管理。静态博客有着轻量、快捷、安全等优势，配合以 Git 版本控制系统与 Github 托管服务，使写作更为方便流畅。

常见的静态博客系统有 [Jekyll][#jekyll] (Ruby)，[Octopress][#octopress] (Ruby)，[Hexo][#hexo] (Node.js)等，更多的系统请参见[这里][#static-blog]。

静态博客程序通常使用 [Markdown][#markdown] 格式编辑页面和文章，然后通过程序生成 HTML 静态页面，通过 Git 命令推送至 Github 上，由 `username.github.io` 子域名进行访问。

### Hugo 是啥
[Hugo][#hugo] 是基于 Go 语言的静态博客系统。

### 安装 Hugo
Hugo 的安装非常简单，只要下载对应系统的可执行文件即可，点击[这里][#hugo_release]下载，下载完成后添加至 `PATH` 即可。如果你是 Mac 用户并使用 `homebrew` 只需执行如下命令：
```
brew install hugo
```

### 本地搭建
创建页面：
```
$ hugo new about.md
```

创建文章：
```
$ hugo new post/first.md
```

下载全部主题：
```
$ git clone --recursive https://github.com/spf13/hugoThemes themes
```

下载单个主题：
```
$ mkdir themes
$ cd themes
$ git clone URL_TO_THEME
```

运行 Hugo：
```
$ hugo server --theme=hyde --buildDrafts
```


### 部署至 Github

在 Github 上搭建个人博客需要注意两点：

* 你必须使用 `username.github.io` 创建 repo
* `master` 分支下的内容将被用来搭建发布 Github Pages

我们将创建两个 repo，一个用来存放 Hugo 的内容，一个 git 子模块存放 `public` 的内容。

1. 创建 GitHub `<your-project>-hugo` 仓库 (它将存储 Hugo 的内容)
2. 创建 GitHub `<username>.github.io` 仓库 (它将存储 `public` 文件夹: 即网站的静态版本)
3. `git clone <<your-project>-hugo-url> && cd <your-project>-hugo`
4. 在本地运行网站 (`hugo serve --watch -t <yourtheme>`)
5. 如果你对结果满意, `Ctrl+C` (kill server) and `rm -rf public` (别着急，你可以通过 `hugo -t <yourtheme>` 重新生成)
6. `git submodule add git@github.com:<username>/<username>.github.io.git public`
7. 基本完成: 创建 `deploy.sh` 脚本实现自动化管理 (别忘了给它可执行权限: `chmod +x deploy.sh`):







[#hugo]:http://gohugo.io/
[#jekyll]:https://github.com/jekyll/jekyll
[#octopress]:https://github.com/imathis/octopress
[#hexo]:https://github.com/hexojs/hexo
[#hugo_release]:https://github.com/spf13/hugo/releases
[#static-blog]:http://www.chinaz.com/special/static-blog/index.html
[#markdown]:http://wowubuntu.com/markdown/basic.html