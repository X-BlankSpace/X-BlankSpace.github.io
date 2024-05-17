---
title: 利用分支备份博客源代码 & 利用 GitHub Pages 展示项目
date: 2017-03-20 10:40:43
toc: true
categories: Learning
tags: [Guide, GitHub]
---
使用 hexo ，如果「换了电脑」或者「博客源代码丢失」了怎么方便地更新博客？

其实 hexo 生成的文件里有 `.gitignore`，本意应该也是想我们把这些文件 push 到 GitHub 上。利用分支备份博客源代码，原理很简单，就是在博客对应的 Repository 里添加一个分支 backup，把源文件 push 到这个分支上，把 hexo 生成的静态网页文件 deploy 在 master 分支上。

<!--more-->
## 利用分支备份博客源代码

### 备份源码
- 在博客对应的 Repository 里找到 Branches 按钮，在搜索框里输入 backup ，创建一个新的分支
- 在 `setting--Branches--Default branch` 中将 backup 设置为默认的分支，这样 git push 时就会 push 到这个分支上
- 在本地任意新建一个文件夹， clone 博客对应的 Repository 到这里，复制 .git 文件夹到你的本地博客的 hexo 文件夹下，并把 hexo 文件夹下的 _config.yml 里的 `deploy--branch` 参数修改为 master  
- 在 git 客户端中定位到 hexo 文件夹，使用命令 `git checkout -b backup` 新建本地分支 backup ,然后 `git push` 即可
<!--more-->

### 更新博客
- 对博客进行更新
- 依次执行 `git add .`、 `git commit -m "some message"`、 `git push` 推送源文件
- 依次执行 `hexo cl`、 `hexo g`、 `hexo d` 生成、部署博客

## 利用 GitHub Pages 展示项目
找到要展示的项目所在的 Repository ，在 `setting--GitHub Pages` 里修改 source 中的 branch 为项目所在的分支（master 或其他分支），并保存设置。选择一个 Theme 或者不选

接着上方出现了一个网址，即可通过 `https://YourGitHubName.github.io/RepositoryName` 访问你的项目。
一个 Repository 里可以存放多个项目，访问时在上面的网址后添加相对路径即可。若要访问单个文件，直接加上该文件的相对路径，也可以将 HTML 文件重命名为 `index.html`，再添加其所在项目文件夹的相对路径即可。
