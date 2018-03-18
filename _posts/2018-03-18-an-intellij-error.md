---
layout: post
title:  "一个Intellij编译错误的理解"
categories: Tool
tags:  Intellij compile-error
author: Eric
---

* content
{:toc}

最近手贱改了Intellij的各种配置，在编译一个项目时，出现了一个奇怪的编译错误：

![index](http://static.zybuluo.com/comeon0r/5vg1whmnctbb4c3d9xxuhsxx/Screen%20Shot%202018-03-14%20at%201.57.39%20PM.png)


打开源文件，FastDrain这个object肯定只定义了一次，而且最近没有修改过它，所以基本断定是Intellij设置的原因导致的编译错误。

问题出在哪呢，打开Project Structure，可以看到Source Folder里有两个sources，估计Intellij编译时会判定为重复：

![index](http://static.zybuluo.com/comeon0r/1g1m7mtu23urljmmera19qyb/Screen%20Shot%202018-03-14%20at%201.54.55%20PM.png)

去掉一个source，编译成功。

