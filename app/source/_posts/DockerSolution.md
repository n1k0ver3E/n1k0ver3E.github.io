---
title: docker 学习笔记——解决Ubuntu中文乱码问题
date: 2017-10-30 04:01:21
categories: Docker
tags: Docker
---
## 0x00 Before writing
自己现在做的项目使用了docker，在进行数据库数据交互的时候发现并不支持中文，但是重新修改Dockerfile明显十分麻烦，后来结合了各种给出了解决方案。但我仍然推荐如果需要支持中文，在Dockerfile中进行环境变量的配置，这样比较方便省心。

> 当前环境：
> Docker：Docker version 17.09.0-ce, build afdb6d4
> Image:Ubuntu 16.04

<!-- more -->
## 0x01 解决Ubuntu中文乱码问题
### 1.在配置image时，永久修改

```
在DOCKERFILE中添加：ENV LANG C.UTF-8
```


### 2.在运行的Container中，进行修改
先输入**locale -a**，查看一下现在已安装的语言


若不存在如**zh_CN**之类的语言包，进行中文语言包安装

```
apt-get install language-pack-zh-hans
```

然后添加中文支持

```
locale-gen zh_CN.UTF-8
```

**安装好后我们可以进行临时修改：**

```
> export LC_ALL='zh_CN.utf8'
> locale
```

**如果想进行永久修改：**

打开/etc的环境变量配置文件，这里的环境是ubuntu 16.04

```
> cd /etc
> /etc# vim bash.bashrc
```

在**bash.bashrc**中写入

```
export LC_ALL='zh_CN.UTF-8'
```


修改后进行执行, 可以使变量设置在当前窗口立即生效

```
> source bash.bashrc
```

最后在重启这个container,对每个新终端窗口都生效

```
docker restart <container Id>
```

重启后``locale``发现已经修改成功



## 0x02 Reference
1. 设置Linux环境变量的方法和区别_Ubuntu
2. ubuntu服务器中设置locale为中文仍然无效的解决方法
