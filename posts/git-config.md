```bash
[core]
    editor = vim
    autocrlf = false
    safecrlf = true
    fileMode = false
[alias]
    log1 = log --graph --abbrev-commit --decorate --date=iso-strict \
        --format=format:'%h - %ad (%ar)%d - %an%n\
        ''          %s'
    log2 = log --graph --abbrev-commit --decorate --date=iso-strict \
        --format=format:'%C(bold blue)%h%C(reset) \
        ''- %C(bold cyan)%ad [%cd]%C(reset) %C(bold green)(%ar)%C(reset) \
        ''%C(bold yellow)%d%C(reset) %C(dim white)- %an%C(reset)%n\
        ''          %C(white)%s%C(reset)'
    log3 = log --graph --abbrev-commit --decorate --date=iso-strict \
        -p -M --follow --stat \
        --format=format:'%C(bold blue)%h%C(reset) \
        ''- %C(bold cyan)%ad [%cd]%C(reset) %C(bold green)(%ar)%C(reset) \
        ''%C(bold yellow)%d%C(reset) %C(dim white)- %an%C(reset)%n\
        ''          %C(white)%s%C(reset)'
    log4 = log --pretty=tformat: --numstat
[user]
	email = zhixing.han.0409@gmail.com
	name = Zhixing Han
[commit]
    template = ~/.gitmessage
[rerere]
	enabled = true
```
