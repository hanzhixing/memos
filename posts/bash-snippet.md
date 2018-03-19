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
CURRENT_FILE="${BASH_SOURCE[0]}"
while [ -h "$CURRENT_FILE" ]; do
  CURRENT_DIR="$(cd -P $(dirname $CURRENT_FILE) && pwd)"
  CURRENT_FILE="$(readlink $CURRENT_FILE)"
  [[ $CURRENT_FILE != /* ]] && CURRENT_FILE="$CURRENT_DIR/$CURRENT_FILE"
done

ROOT_PATH="$(cd -P $(dirname $CURRENT_FILE) && pwd)"
```
