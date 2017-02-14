[返回首页](../README.md)

# 有用的Bash代码片段

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
