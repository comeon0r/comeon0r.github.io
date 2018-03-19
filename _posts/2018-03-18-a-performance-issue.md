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

背景：年前一直在integrate一个广告推荐模块，调用的是一个external service。依照惯例，在立项之前就做了性能测试，效果还是不错滴。

![index](http://static.zybuluo.com/comeon0r/dulolwh47ytb1tre05ie9joj/image001.png)

可以看到，p75的response time大概是75ms，经过计算，能满足我们的上线要求。

项目完成之后，简单地又做了性能测试，结果也不错，过年就开心地玩耍去了。

年后回来又测了一遍，傻眼了，performance degradation非常明显：

![index](http://static.zybuluo.com/comeon0r/m1diqob5o7c5lx6m0barzcgx/image006.png)

p75下的response time退化到了748ms，更别提p95了。这种情况是万万不能上线的。

开始查找原因！

分析1：
