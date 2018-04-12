---
layout: post
title:  "mac error: missing xcrun"
categories: Frontend
tags:  mac error xcrun
author: Eric
---

* content
{:toc}

更新了mac，git就不能用了。显示“missing xcrun at: XXX.../xcrun”:

![index](http://static.zybuluo.com/comeon0r/tezx29wcmh44wb0n9r9y1qsu/Screen%20Shot%202018-03-28%20at%209.50.21%20AM.png)

原因是，每次更新，苹果都要卸载掉XCode，而Mac上的git，是跟着XCode，一起安装的。

在终端输入：

```
xcode-select --install
```

![index](http://static.zybuluo.com/comeon0r/4ph3vtw9u1hwgxlsjfhnmn55/Screen%20Shot%202018-03-28%20at%209.50.34%20AM.png)

安装XCode就可以解决了。
