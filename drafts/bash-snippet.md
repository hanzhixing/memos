---
layout: post
title:  "有用的Bash小技巧"
date:   2015-12-25 10:24:54
categories: Shell
tags:
    - linux commands
---

## 检查一个进程是否在存在
```bash
$ kill -p $PID &>/dev/null
```

```bash
$ ps -p $PID | grep -q $PID
```
```bash
$ test -x /proc/$PID
```

## 获取当前路径
```bash
#!/bin/bash
DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" > /dev/null && pwd)"
```
