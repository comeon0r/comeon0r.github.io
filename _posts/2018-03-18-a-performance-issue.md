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

# 背景

背景：年前一直在integrate一个广告推荐模块，调用的是一个external service。依照惯例，在立项之前就做了性能测试，效果还是不错滴。

![index](http://static.zybuluo.com/comeon0r/dulolwh47ytb1tre05ie9joj/image001.png)

可以看到，p75的response time大概是75ms，经过计算，能满足我们的上线要求。

项目完成之后，简单地又做了性能测试，结果也不错，过年就开心地玩耍去了。

年后回来又测了一遍，傻眼了，performance degradation非常明显：

![index](http://static.zybuluo.com/comeon0r/m1diqob5o7c5lx6m0barzcgx/image006.png)

p75下的response time退化到了748ms，更别提p95了。这种情况是万万不能上线的。

开始查找原因！

分析1：是因为这个external service存在缺陷，会因call number的提升而导致明显的performance degradation？

看起来不像，因为在立项前的测试中，我在系统中直接call这个external service，用的是全量数据，call number从图中看明显比年后的压力测试更大，然而性能却更好。否决！

分析2：当前external service存在性能问题？

紧急联系了对方的邮件组，回应速度非常快：“We are investigating, but so far I don’t see any issues。。。”，还附了一张图：

![index](http://static.zybuluo.com/comeon0r/ckx6vth6xdx67ntdxaj2vxat/image005.png)

简而言之就是————“哥，我们好着呢，不是我们的问题！”。

这还得了，一边联系对方继续排查，还schedule了一个非常早的会议（毕竟对方在美国），不放过任何可能的机会，一边继续查找自己的原因。

分析3：Gafana UI的显示问题？

既然external service宣称自己的system没出问题，难道是我们自己踢了个乌龙？赶紧给系统打了几个log，看下真实的response time，是不是真的系统性能这么差。

事实是，response time确实在几百毫秒左右，是真差！

分析4：网络端口阻塞？

因为我们的系统用了很多的actors，如果同时有多个actor同时通过不同的端口号发送数据，是会导致严重的性能下降。

这时候，做了两件事：

1）检查代码，是否有申请多个端口，或浪费端口的情况，结论：并没有。

2）尝试关闭系统里其他的service calling，减少端口占用的情况，看是否有缓解，结论：并没有。

## 柳暗花明

毫无办法之际，继续看了下log，突发奇想对每秒的call number进行了下统计：

```
$ grep 'bullseye sending' lina-test-mobile.out | grep "20:35:00" | wc -l
1454
```

很正常，看看一下秒呢：

```
$ grep 'bullseye sending' lina-test-mobile.out | grep "20:35:01" | wc -l
0
```

纳尼，为什么01秒时候的call number这么少！

再下一秒呢？

```
$ grep 'bullseye sending' lina-test-mobile.out | grep "20:35:02" | wc -l
0
```

也是没有任何calling!

看一整分钟呢？

```
$ grep 'bullseye sending' lina-test-mobile.out | grep "20:35:.*" | wc -l
1454
```

答案已经很明显了：虽然从Ganafa UI上看，我们的call number在20~30左右，但这其实是一分钟之内的平均值，事实是，因为某种原因，我们的系统在这一分钟内的calls全部集中在了一秒内发送，这导致了很严重的网络拥堵问题，难怪性能会变这么差！

回过头来再检查代码，发现是系统设计的问题，我们会将输入数据按照某种规则分组，相同的组的数据会通过一个actor-dispactcher申请到大概2500个actor来同时跑，这会导致相同类型的数据几乎是同时跑，也就是会同时调用某个external service。

验证一下，把actor-dispactcher的actor number改为一个很小的数，比如100，性能马上就变好了：

![index](http://static.zybuluo.com/comeon0r/ou6getnxukxle68zxa7gg4by/Screen%20Shot%202018-03-10%20at%204.18.18%20PM.png)

那为什么之前没有发现这个问题呢？

因为立项之前的测试刚好没用到这个架构，所以calling是均匀分布在每秒里的，因此虽然call number很大但是response time仍然很小。然而，项目完成之后，压力测试放在整个系统中做，原来的系统架构的弊端就显现出来了。

## 经验与教训

解决方案就不说了，不是本文重点。

一点反思：

1. 不要有迷信心理，任何系统问题一定有一个极度stupid的原因，不抛弃，不放弃，总能找到它。

2. 做对比测试需要尽可能地控制好“不变量”，最好是“唯一不变量”，虽然这很困难，特别是上线压力特别大的时候。

3. 借助工具固然好，但解决问题的时候，有时候需要跳出这些好用的工具，多从不同的维度去考虑它。
