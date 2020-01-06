---
title: Git方式提交代码的超详细教程
date: 2017-03-06 05:22:28
categories: Git
tags: Git
---
## 0x00 写在前面
一直以来Git提交都是Github初学者的拦路虎，我也是经过了那个时期之后才比较清楚和快速的进行代码托管在Github上，这同样也是一份Command备份在自己忘掉的时候比较快速的回忆起来。希望能够帮助到在看这篇文章的你。有建议和问题欢迎再评论框进行交流。O(∩_

>macOS Sierra version 10.12.3
>Terminal:Iterm2

## 0x01 正文
### Step0 注册账号并创建SSH密钥
首先我们来创建SSH密钥连接Github服务器和本地。
在控制台输入

```
ssh-keygen -C 'your@email.address' -t rsa   --输入你的邮箱地址
```

之后会弹出几行英文，直接回车即可。(作用是来确认使用默认路径)
之后出现一块SHA的key。这说明已经创建成功。
现在我们要提交key到Github上去。

<!--more-->

```
vim ~/.ssh/id_rsa.pub
```
打开后可以看一串秘钥。以ssh-rsa开头，邮箱地址为结尾。将它复制到``Setting-SSH and GPG keys-new SSH key-``中粘贴并提交。


### Step1 建一个新的仓库(repository)
直接**Create New Repository**
创建的时候可以选上**Initialize this repository with a README**
这样可以使后面少一个步骤，并且Github也推荐在仓库中写有README

### Step2 克隆仓库
在初始化版本库之前，可先确认公钥是否正确。

```
ssh -T git@github.com
```

这时会提醒一个Warning，It doesn’t matter.

接下来对库进行clone：

```
git clone https://github.com/PYF0311/justademo.git
```

clone成功会显示：

```
Cloning into 'justademo'...
Warning: Permanently added 'github.com,207.97.227.239' (RSA) to the list of known hosts.
remote: Counting objects: 3, done.
remote: Total 3 (delta 0), reused 0 (delta 0)
Receiving objects: 100% (3/3), done.
```

### Step3 上传README.md
我们要先上传README.md否则会出现一些错误。如果在之前我们已经勾选生成README.md, 那么就可以跳过这一步。

```
git init
touch README.md
git add README.md
git commit -m 'first_commit'
git remote add origin https://github.com/PYF0311/justademo.git
git push origin master
```

### Step4 上传文件

```
git add .
git commit -m 'first_commit'   --first_commit是替换为你要写的版本信息或者一个简单介绍
git remote add origin https://github.com/PYF0311/justademo.git
git push origin master
```

## 0x02 其他报错解决方案:
#### 1.执行``git remote add origin https://github.com/PYF0311/justademo.git``出现错误：

fatal:remote origin already exists
则执行以下语句：

```
git remote rm origin
```

#### 2.执行``git push origin master``时，报错：

```
error:failed to push som refs to...
```
则执行以下语句：

```
git pull -f origin master     //利用强覆盖方式用你本地的代码替代git仓库内的内容
```

现将远程服务器(remote)上面的文件拉回来，再push上去。

#### 3.如果出现不能push到Github上,并报错：

```
cannot push to github: everything up-to-date
```

则执行以下语句：

```
git commit -m 'first_commit' --first_commit替代为你要提交的文件
git push origin master
```

## 0x03 PS
如果你只是临时性上传一个单个文件的话，同时也可以不用通过git上传。

1. 在建立仓库的时候同时点选README.md

2. 在仓库详情中能看到一个update file，之后就会跳转页面，我们可以看到Drag files here to add them to your repository。通过拖拽即可完成代码上传。


## 0x04 Quote
1. http://www.cnblogs.com/findingsea/archive/2012/08/27/2654549.html

2. http://artori.us/git-github-usage/

3. http://stackoverflow.com/questions/11084515/cannot-push-to-github-everything-up-to-date
