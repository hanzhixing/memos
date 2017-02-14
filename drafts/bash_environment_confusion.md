---
layout: post
title:  "让人迷惑的Bash环境"
date:   2016-06-07 12:09:21
categories: Shell
tags:
    - bash
---

因为不同定义下的环境是有差异的，因为它们会加载不同的bash脚本来初始化环境。
知道了初始化的时候加载那些bash脚本，你才能够在正确的脚本中加入正确的代码。
首先要知道所有的bash脚本都会用/bin/bash来执行的。

## login/non-login
简单来说，当你登录一个shell的时候提示输入用户名密码，那么登录进去就会是login shell。
在Linux桌面环境打开Terminal的时候，它是不会让你输入用户名密码，因此它是non-login shell，但是桌面本身是login shell。
实际上是调用bash命令的时候传入什么参数决定的。
下面的命令可以检查当前的shell环境是否是login shell

```bash
$ shopt -q login_shell && echo 'Login shell' || echo 'Not login shell'
```

## interactive/non-interactive
这个比较简单，人机交互方式使用的shell就是interactive shell，比如使用终端的时候终端本身的环境。
而执行bash脚本文件过程当中的那个环境就是non-interactive shell。

To check if you are in a login shell:
```bash
$ [[ $- == *i* ]] && echo 'Interactive' || echo 'Not interactive'
```
$-变量中是会保存调用shell的时候带的参数。用*i*来检查是否包含i是因为bash -i表示interactive。可以查看man bash验证这一点。

## 初始化脚本加载顺序
[直接看Bash的官方文档](http://www.gnu.org/software/bash/manual/bashref.html#Bash-Startup-Files)

### login shell
1. /etc/profile
2. ~/.bash_profile
3. ~/.bash_login
4. ~/.profile

### non-login shell
1. ~/.bashrc

### non-interactive shell
1. 继承父进程的环境($BASH_ENV目前还没太理解在什么时刻被赋)
