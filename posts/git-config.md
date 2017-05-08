```bash
[core]
    editor = vim
    autocrlf = false
    safecrlf = true
    fileMode = false
[alias]
    log1 = log --graph --abbrev-commit --decorate --all --date=iso --format=format:'%h %ad | %s%d [%an]'
    log2 = log --graph --abbrev-commit --decorate --all --date=iso --format=format:'%C(bold blue)%h%C(reset) - %C(bold green)(%ar)%C(reset)                   %C(white)%s%C(reset) %C(dim white)- %an%C(reset)%C(bold yellow)%d%C(reset)'
    log3 = log --graph --abbrev-commit --decorate --all --date=iso --format=format:'%C(bold blue)%h%C(reset) - %C(bold cyan)%aD%C(reset) %C(bold              green)(%ar)%C(reset)%C(bold yellow)%d%C(reset)%n''          %C(white)%s%C(reset) %C(dim white)- %an%C(reset)'
```
