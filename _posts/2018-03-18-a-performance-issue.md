---
layout: post
title:  "一个Performance Issue的解决与反思"
categories: Big-Data
tags:  performance big-data grafana issue
author: Eric
---

* content
{:toc}

这两天被一个performance issue搞得精疲力尽，其中走了不少弯路，最终才柳暗花明，是为记。

背景：年前integrate了一个广告推荐模块，调用的是一个external service，年前依照惯例做了性能测试，效果还是不错滴。

![index](http://static.zybuluo.com/comeon0r/dulolwh47ytb1tre05ie9joj/image001.png)


