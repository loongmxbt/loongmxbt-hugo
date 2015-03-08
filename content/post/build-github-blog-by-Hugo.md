+++
date = "2015-03-04T11:21:04+08:00"
draft = false
title = "使用 Hugo 搭建 Github 静态博客"
tags = ["Hugo", "Go", "Github", "静态博客"]
categories = ["Web"]
+++

### Github 静态博客是啥

自从 Github 推出 Pages 服务以来，静态博客程序就层出不穷。所谓『静态博客』，就是所有页面均为 HTML 静态页面的博客。与常见的 Wordpress 等动态博客系统不同，它没有数据库，没有动态语言，没有复杂的后台管理。静态博客有着轻量、快捷、安全等优势，配合以 Git 版本控制系统与 Github 托管服务，使写作更为方便流畅。

常见的静态博客系统有 [Jekyll][#jekyll] (Ruby)，[Octopress][#octopress] (Ruby)，[Hexo][#hexo] (Node.js)等，更多的系统请参见[这里][#static-blog]。

静态博客程序通常使用 [Markdown][#markdown] 格式编辑页面和文章，然后通过程序生成 HTML 静态页面，通过 Git 命令推送至 Github 上，由 `username.github.io` 子域名进行访问。

### Hugo 是啥
[Hugo][#hugo] 是基于 Go 语言的静态博客系统。

### 安装 Hugo
Hugo 的安装非常简单，只要下载对应系统的可执行文件即可，点击[这里][#hugo_release]下载，下载完成后添加至 `PATH` 即可。如果你是 Mac 用户并使用 `homebrew` 只需执行如下命令：
```bash
$ brew install hugo
```

### 本地搭建
创建页面：
```bash
$ hugo new about.md
```

创建文章：
```bash
$ hugo new post/first.md
```

下载全部主题：
```bash
$ git clone --recursive https://github.com/spf13/hugoThemes themes
```

下载单个主题：
```bash
$ mkdir themes
$ cd themes
$ git clone URL_TO_THEME
```

运行 Hugo：
```bash
$ hugo server --theme=hyde --buildDrafts
```
或者
```bash
$ hugo server -t nofancy
```

注意，默认创建的文章页面有 `draft=true` 选项，设置为 false 才可以不通过 `--buildDrafts` 选项生成。


### 部署至 Github

在 Github 上搭建个人博客需要注意两点：

* 你必须使用 `username.github.io` 创建 repo
* `master` 分支下的内容将被用来搭建发布 Github Pages

我们将创建两个 repo，一个用来存放 Hugo 的内容，一个 git 子模块存放 `public` 的内容。

1. 创建 GitHub `<your-project>-hugo` 仓库 (它将存储 Hugo 的内容，注意将 `public` 目录添加至 `.gitignore` 中)
2. 创建 GitHub `<username>.github.io` 仓库 (它将存储 `public` 文件夹: 即网站的静态版本)
3. `git clone <<your-project>-hugo-url> && cd <your-project>-hugo`
4. 在本地运行网站 (`hugo server --watch -t <yourtheme>`)
5. 如果你对结果满意, `Ctrl+C` (kill server) and `rm -rf public` (别着急，你可以通过 `hugo -t <yourtheme>` 重新生成)
6. `git submodule add git@github.com:<username>/<username>.github.io.git public`
7. 基本完成: 创建 `deploy.sh` 脚本实现自动化管理 (别忘了给它可执行权限: `chmod +x deploy.sh`):

```bash
#!/bin/bash

echo -e "\033[0;32mDeploying updates to GitHub...\033[0m"

# Build the project. 
hugo # if using a theme, replace by `hugo -t <yourtheme>`

# Go To Public folder
cd public
# Add changes to git.
git add -A

# Commit changes.
msg="rebuilding site `date`"
if [ $# -eq 1 ]
  then msg="$1"
fi
git commit -m "$msg"

# Push source and build repos.
git push origin master

# Come Back
cd ..
```

### 增加代码高亮支持

将以下代码添加至 `themes/theme-name/layouts/partials/headerer.html`

```html
<link rel="stylesheet" href="https://yandex.st/highlightjs/8.0/styles/default.min.css">
<script src="https://yandex.st/highlightjs/8.0/highlight.min.js"></script>
<script>hljs.initHighlightingOnLoad();</script>
```

### 增加数学公式支持

[这篇文章][#mathjax]有详细的 MathJax 和 LaTeX 简介。

将以下代码添加至 `themes/theme-name/layouts/partials/footer.html`

```html
<script type="text/javascript"
  src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>

<script type="text/x-mathjax-config">
MathJax.Hub.Config({
  tex2jax: {
    inlineMath: [['$','$'], ['\\(','\\)']],
    displayMath: [['$$','$$'], ['\[','\]']],
    processEscapes: true,
    processEnvironments: true,
    skipTags: ['script', 'noscript', 'style', 'textarea', 'pre'],
    TeX: { equationNumbers: { autoNumber: "AMS" },
         extensions: ["AMSmath.js", "AMSsymbols.js"] }
  }
});
</script>

<script type="text/x-mathjax-config">
  MathJax.Hub.Queue(function() {
    // Fix <code> tags after MathJax finishes running. This is a
    // hack to overcome a shortcoming of Markdown. Discussion at
    // https://github.com/mojombo/jekyll/issues/199
    var all = MathJax.Hub.getAllJax(), i;
    for(i = 0; i < all.length; i += 1) {
        all[i].SourceElement().parentNode.className += ' has-jax';
    }
});
</script>
```

增加相应的 css

```css
code.has-jax {
    font: inherit;
    font-size: 100%;
    background: inherit;
    border: inherit;
    color: #515151;
}
```

使用方法：

若 Markdown 语法与 MathJax 语法冲突时，对于 inline 公式，可以在原先两个`$`之外加上两个`` ` ``代表 code。对于 display 公式，可以在原先两个`$$`之外加上`<div>`和`</div>`。这样所有的公式就可以仅执行 MathJax 解析了。

[#hugo]:http://gohugo.io/
[#jekyll]:https://github.com/jekyll/jekyll
[#octopress]:https://github.com/imathis/octopress
[#hexo]:https://github.com/hexojs/hexo
[#hugo_release]:https://github.com/spf13/hugo/releases
[#static-blog]:http://www.chinaz.com/special/static-blog/index.html
[#markdown]:http://wowubuntu.com/markdown/basic.html
[#mathjax]:http://mlworks.cn/posts/introduction-to-mathjax-and-latex-expression/