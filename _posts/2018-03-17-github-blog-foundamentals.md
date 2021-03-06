---
layout: post
title:  "github建博客的原理"
categories: Frontend
tags:  github blog pages jekyll
author: Eric
---

* content
{:toc}

# Github Pages + Jekyll

通过github建博客是真正的“速食”技术，大概花费不到10分钟。

为何如此便利呢，透过现象看本质，Github搭建的博客通常只是Github Pages + Jekyll 而已。

# Github Pages是什么

[Github Pages](https://pages.github.com/)用Github官网的原话说，是“Websites for you and your projects. Hosted directly from your GitHub repository. Just edit, push, and your changes are live.” 

说白了是Github推出的由用户编写的、托管在github上的静态网页。它的解决的痛点是，用户的repo通常由大量的代码组成，越复杂的repo越让人无从下手，这时候你可以给自己的repo建一个静态网站，小白也能看得懂了。

问题来了，不是已经有了README.md了吗？README.md只是一个文件，而Github Pages支持站内生成网页。

Github Pages的网页还会经过Jekyll的再处理，这使得它能提供更丰富的功能。


# Jekyll是什么

[Jekyll](https://jekyllrb.com/)是a popular static site generator designed for blogging and software documentation，它是Github默认的模板系统。

什么叫static site generator（静态站点生成器）？比如你可以根据Jekyll的规范，写出一些网页源码，static site generator会根据这些网页源码生成静态文件，Jekyll还提供了模板、变量、插件等功能。

通过Jekyll在github pages上创建的博客具体有哪些规范呢？ 比如

- 配置文件名为_config.yml
- 模板文件放在_layouts目录
- Jekyll使用Liquid模板语言，{{ page.title }}表示文章标题，{{ content }}表示文章内容，等等。
- blog文章存放在_posts目录。


# 其他

那么，如何在github上找到合适自己的Github Blog Theme呢，一个很简单的技巧是，在github上搜索比如“jekyll github.io”或者“jekyll blog”之类的关键词：

![index](http://static.zybuluo.com/comeon0r/yjzodrs4ejlc02kg4r8i6tuc/Screen%20Shot%202018-03-18%20at%202.15.45%20PM.png)
