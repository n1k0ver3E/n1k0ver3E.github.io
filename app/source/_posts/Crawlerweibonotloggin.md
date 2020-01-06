---
title: 简单的免登陆抓取新浪微博数据
date: 2017-08-03 03:01:10
categories: Crawler
tags:
- Python
---
## 0x00 写在前面
饭偶像之后承担了写群机器人的工作，所以最近一直在鼓弄爬虫，写了集资实时监控，现在是微博监控，之后还要写其他东西…库亚西。

之前就看过很多爬微博的一些文章，不过大多需要模拟登录，或者是调用weibo的api，后来貌似API权限逐渐降低，也就变得不实用了。。。

手机版weibo``http://m.weibo.com``后加uid便可以进行博文。

这个爬虫的小程序是通过手机版weibo``http://m.weibo.com``来进行模拟请求，不是通过分析html代码再通过筛选来完成数据抓取。
简单的介绍就是这样，后面是正文！

<!-- more -->

## 0x01 正文

### STEP 1. 准备工作
#### 1. 获取微博用户的对应Id

>使用的工具是：Chrome 开发者工具
>
>Windows下快捷键：F12
>
>MacOS下快捷键：alt+cmd+i

打开开发者工具-选中network-在左边选中带有``getIndex?``的包


获取我们所需要的``uid``和``containerId``.

PS:虽然Query String字段有很多项，但仅需要uid和containerId就可以定位到置顶微博用户。

#### 2. 寻找所需要的请求


可见它是有很多JSON数据在一起的请求包


而微博正文就隐藏在这个请求包中的mblog中



### STEP 2. 原始数据获取及转码

```python
import requests
url="https://m.weibo.cn/api/container/getIndex"
payload={'type':'uid','value':'5879771257','containerid':'1076035879771257'}
r=requests.post(url,data=payload)
raw_text=r.text
```
通过requests写一个简单的请求模拟.py3这一点还是很棒的，不需要额外unicode转成中文。
如果是py2的话还要``raw_text=r.text.encode().decode()``
encode之后是byte格式，decode使其变成str格式。

### STEP 3. JSON转换成dict
数据本来就是JSON对象。通过``json.loads()``转换为python的数据字典dict.
再通过dict的定位就可以抓取到微博内容了。



## 0x02 代码
```
#原始URL:https://m.weibo.cn/api/container/getIndex?type=uid&value=5931004865&containerid=1076035931004865
import requests
import json
url="https://m.weibo.cn/api/container/getIndex"
payload={'type':'uid','value':'5879771257','containerid':'1076035879771257'}
r=requests.post(url,data=payload)
raw_text=r.text
d=json.loads(raw_text)
for i in range(10):
    print(d["cards"][i]["mblog"]["text"])
```

## 0x03 PS
1.不仅仅是博文内容，个人资料等只要出现在m.weibo.com的内容均可以爬取下来。

2.一个请求包含最多10条微博，可通过再末尾加入page页数来继续循环模拟请求。

```
example:
https://m.weibo.cn/api/container/getIndex?type=uid&value=5879771257&containerid=1076035879771257&page=2
```

3.有其他问题可以在评论框提出。尚未发现banIP的情况，会一直跟进下去。
