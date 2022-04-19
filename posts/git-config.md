```bash
[core]
    editor = vim
    autocrlf = false
    safecrlf = true
    fileMode = false
[alias]
    log1 = log --all --graph --abbrev-commit --decorate --date=iso-strict \
        --format=format:'%C(bold blue)%h%C(reset) \
        ''- %C(bold cyan)%ad [%cd]%C(reset) %C(bold green)(%ar)%C(reset) \
        ''%C(bold yellow)%d%C(reset) %C(dim white)- %an%C(reset)%n\
        ''          %C(white)%s%C(reset)'
    log2 = log --all --graph --pretty=format:'%C(auto)%h%d %s \
        ''%C(dim white) - %ar - %aN%Creset'
    log3 = log --graph --abbrev-commit --decorate --date=iso-strict \
        -p -M --follow --stat \
        --format=format:'%C(bold blue)%h%C(reset) \
        ''- %C(bold cyan)%ad [%cd]%C(reset) %C(bold green)(%ar)%C(reset) \
        ''%C(bold yellow)%d%C(reset) %C(dim white)- %an%C(reset)%n\
        ''          %C(white)%s%C(reset)'
    # git log --numstat --pretty="%H" --author="Your Name" commit1..commit2 | awk 'NF==3 {plus+=$1; minus+=$2} END {printf("+%d, -%d\n", plus, minus)}'
    log4 = log --pretty=tformat: --numstat
[user]
    email = zhixing.han.0409@gmail.com
    name = Zhixing Han
[commit]
    template = ~/.gitmessage
[rerere]
    enabled = true
```
