[返回首页](../README.md)

# 开发工具配置列表

## git log
```bash
[core]
    editor = vim
    autocrlf = false
    safecrlf = true
    fileMode = false
[alias]
    log1 = log --graph --abbrev-commit --decorate --all --date=iso --format=format:'%h - %ad (%ar)%d - %an%n\
        ''          %s'
    log2 = log --graph --abbrev-commit --decorate --all --date=iso --format=format:'%C(bold blue)%h%C(reset) \
        ''- %C(bold cyan)%ad%C(reset) %C(bold green)(%ar)%C(reset) \
        ''%C(bold yellow)%d%C(reset) %C(dim white)- %an%C(reset)%n\
        ''          %C(white)%s%C(reset)'
```

