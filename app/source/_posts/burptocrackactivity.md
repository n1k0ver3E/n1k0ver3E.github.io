---
title: 记一次有意思的burp抓包经历
date: 2017-09-24 02:05:40
categories: Hacker
tags:
- Python
- Burpsuite
- Hacker
---
## 0x00 Before Writing.
又是一个不断电不断网的周末夜晚。我又打开了某手游…发现有个抽奖活动！！
通过奖品兑换cdkey拿一些奖品。

抽奖盘大概类似这样的东西⬇️ ⬇️
![](https://pic4.zhimg.com/80/v2-1a0e944f384ade0cbfb88896817f3e57_hd.jpg)

左下角有个抽奖次数，上传图片还可以多次抽奖。
但不知道怎么搞的突然意外增加了抽奖次数，让我多抽了一次。这极大的引起了兴趣，感觉这活动页其中好像有些问题，本着打破砂锅问到底的精神，我打开了Burpsuite.
【毕竟人之常情，每个人都想多抽几次嘛…

## 0x01 开始进行抓包分析
刚开始对整个抽奖的过程进行一个抓包分析。
通过Foxyproxy这个浏览器插件对网页进行代理，包信息被burp捕获到，之后send to Repeater.【Foxyproxy十分好用，简直就是配合burp的神器，一键代理十分方便，在chrome和firefox应用商店中均可下载.

<!--more-->
**通过返回的Response包我们发现：**

① 登录后默认有一次抽奖的机会。cishu:1

② 上传文件可以增加抽奖次数cishu++，最高可上传4次就会返回错误信息了。
![](https://pic4.zhimg.com/80/v2-5f43e8c1a9f4c103e00694119ba3b04f_hd.jpg)

![](https://pic4.zhimg.com/80/v2-49615c0421b34110909aae99d1189e3f_hd.jpg)
③ 每次抽奖后cishu会-1，并返回cdkey的值。通过兑换就能拿到我们想要的礼物。
![](https://pic4.zhimg.com/80/v2-b511887273be778f170cc2d07151606b_hd.jpg)
分析之后就开始进行改requests的包，对比以抽奖的cookie和未抽奖的cookie，发现这并不是改包就能完成的,改cookie并不能绕过对现有用户的认证，仍需要重新登录…也不能改cishu，因为cishu并不是在requests包中进行请求的。

## 0x02 注册环节
花费了很长时间研究在改包上，但并没有什么突破。无法通过改cishu来实现我们的目的，那就换个方向去研究一下用户注册以及登录。之后我就去抓一下注册登录的包来观察一番。因为抽奖规则就是新账号注册就有抽奖机会，而这一过程并不检测IP。仅通过手机号进行关联。

将注册的包send to repeater来观察一番验证码的流程，有了很大的收获。

第一次请求: 并没有返回任何值~
![](https://pic2.zhimg.com/80/v2-bab4cd11cce81e0aaa0022bf4767feb1_hd.jpg)



本着打CTF的比赛经验，我又习惯性的请求了第二次：**页面上返回了验证码！**

![](https://pic2.zhimg.com/80/v2-63b1c384c2caf0532a39236004ff6a85_hd.jpg)

分析产生了大突破！response返回了int(255035)。这样我们就能通过get请求来接受返回的response包的信息，得到验证码。这样就突破了注册的限制，通过脚本的编写可以完成之前所进行的操作。

## 0x03 After writing.
之后就是写了python的代码来完成了自动化的过程，添加了random的库随机生成了手机号并通过shell脚本一直运行，收获了很多很多的cdkey，造福了朋友和自己……….

**随机生成手机号的代码：**

```
mobile_nums=[]
for i in range(10):

	ran_num=random.uniform(13,14)*1000000000
	ran_num=str(ran_num)
	ran_mobile=ran_num[:11]
	mobile_nums.append(ran_mobile)

f=open("/Users/Niko/Desktop/cdkey",'a')
f.write("-----------------------------\n")
f.write("mobile_nums="+str(mobile_nums)+"\n")
```

**shell脚本：**

写了shell脚本来循环跑了50次，开了2个进程，吃完饭发现成果颇丰ð~

```
#!/bin/sh
i=0
while [ "$i" !=  "50" ]
do
	python ~.py
	i=$(($i+1))
done
```
本体就是requests库的应用，害怕被顺丰我就不po了…

## 0x04 PS:
1. 一次很有趣的经历让我对网络安全又增加了兴趣，永远保持着好奇心是最重要和有趣的！
2. 不管是作为开发者还是渗透测试人员，都要严格代码规范，以及加强安全性检测和管理。抓了包发现很多变量名都很不formal，某种程度上坚定了我一定要攻克它的决心2333333



感谢围观

*By Niko.*
