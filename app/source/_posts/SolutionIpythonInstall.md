---
title: macOS 下通过pip安装Ipython权限问题解决
date: 2017-02-16 23:41:26
tags:
- Python
- macOS
categories: Python
---
### 0x00 Before writing
安装了``pip``之后就开始安装``ipython``了。使用``sudo pip install ipython``却一直显示*Operation not permitted*这让我非常苦恼。

>本机 macOS Sierra 10.12.3

```
Exception:
Traceback (most recent call last):
  File "/Library/Python/2.7/site-packages/pip/basecommand.py", line 209, in main
    status = self.run(options, args)
  File "/Library/Python/2.7/site-packages/pip/commands/install.py", line 317, in run
    prefix=options.prefix_path,
  File "/Library/Python/2.7/site-packages/pip/req/req_set.py", line 732, in install
    **kwargs
  File "/Library/Python/2.7/site-packages/pip/req/req_install.py", line 835, in install
    self.move_wheel_files(self.source_dir, root=root, prefix=prefix)
  File "/Library/Python/2.7/site-packages/pip/req/req_install.py", line 1030, in move_wheel_files
    isolated=self.isolated,
  File "/Library/Python/2.7/site-packages/pip/wheel.py", line 376, in move_wheel_files
    clobber(source, dest, False, fixer=fixer, filter=filter)
  File "/Library/Python/2.7/site-packages/pip/wheel.py", line 315, in clobber
    ensure_dir(destdir)
  File "/Library/Python/2.7/site-packages/pip/utils/__init__.py", line 83, in ensure_dir
    os.makedirs(path)
  File "/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/os.py", line 150, in makedirs
    makedirs(head, mode)
  File "/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/os.py", line 150, in makedirs
    makedirs(head, mode)
  File "/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/os.py", line 157, in makedirs
    mkdir(name, mode)
OSError: [Errno 1] Operation not permitted: '/System/Library/Frameworks/Python.framework/Versions/2.7/share'
```

提示是权限问题，但是却发现改成root也是不能成功，搁置了一下。

### 0x01 SIP机制
google了很久发现了一个叫做SIP的机制被应用到MAC OSX EI Capitan中增加了一个SIP功能，不管你是不是su，都会阻止你在系统目录下进行操作……

既然是一种机制，那就只能想着去关上就好了。
又找到了解决的办法：

> (1)重启OSX系统，然后按住Command+R

> (2)出现界面之后，选择Utilities menu中Terminal

> (3)在Terminal中输入csrutil disable关闭SIP(csrutil enable打开SIP)

> (4)重启reboot OSX

### 0x02 方式改进
果然上面那种方法看着很难受，不过却从原理上解释了为什么会出现deny。

**不过后来发现这并不是最优雅的方法！！**

``pip install ipython --user -U``

基于用户的权限来安装包。

执行之后会提示：

> Installing collected packages: setuptools
> Successfully installed setuptools-34.2.0

继而安装成功setuptools.
再通过**easy_install ipython**就能成功安装ipython了。

### 0x03 引用

1.http://xiaorui.cc/2016/03/27/解决mac-osx下pip安装ipython权限的问题/

2.http://www.freebuf.com/articles/system/97411.html
