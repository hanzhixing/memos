[返回首页](../README.md)

# 多版本Node.js共存
>解决环境中使用多个Node.js版本的问题。

使用[nvm](https://github.com/creationix/nvm)。

## 安装
如官方文档的说明，尽量用官方提供的方法安装。

[使用命令](https://github.com/creationix/nvm#install-script)
```bash
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.0/install.sh | bash
```

或者

[手动安装](https://github.com/creationix/nvm#manual-install)
```bash
  $ git clone https://github.com/creationix/nvm.git ~/.nvm
  $ cd ~/.nvm
  $ git checkout `git describe --abbrev=0 --tags --match "v[0-9]*" origin`
```


## 首次使用之前的准备工作
在想要使用nvm命令的Bash环境中，需要加载nvm的环境初始化脚本。
```bash
source /.nvm/nvm.sh
```

在中国大陆npm的源访问受限，所以让nvm使用淘宝的npm镜像。
```bash
export NVM_NODEJS_ORG_MIRROR="https://npm.taobao.org/mirrors/node"
```

为了进入终端可以直接使用nvm，建议把上面两行追加到/.bashrc文件中。

```bash
# ~/.bashrc
# ...
source /.nvm/nvm.sh
export NVM_NODEJS_ORG_MIRROR="https://npm.taobao.org/mirrors/node"
```

你可以事先了解一下[Bash环境初始化过程](./bash_environment_initialization.md)。
      
## 选择正确的Node.js版本安装

你应该总是使用[LTS(Long Term Support)](https://github.com/nodejs/LTS#lts-schedule)版本。

使用下面命令可以查看目前最新的LTS版本列表。
```bash
$ nvm ls-remote --lts
->       v4.6.2   (LTS: Argon)
         v4.7.0   (LTS: Argon)
         v4.7.1   (LTS: Argon)
         v4.7.2   (LTS: Argon)
         v4.7.3   (Latest LTS: Argon)
         v6.9.0   (LTS: Boron)
         v6.9.1   (LTS: Boron)
         v6.9.2   (LTS: Boron)
         v6.9.3   (LTS: Boron)
         v6.9.4   (LTS: Boron)
         v6.9.5   (Latest LTS: Boron)
```
在这个列表你可以看到
- 代号为Argon的LTS版本最新版本是v4.7.3
- 代号为Boron的LTS版本最新版本是v6.9.5

安装最新版本
```bash
$ nvm install lts/argon
$ nvm install lts/boron
```

```bash
$ nvm ls
         v4.7.3
->       v6.9.5
default -> lts/argon (-> v4.7.3)
node -> stable (-> v6.9.5) (default)
stable -> 6.9 (-> v6.9.5) (default)
iojs -> N/A (default)
lts/* -> lts/boron (-> v6.9.5)
lts/argon -> v4.7.3
lts/boron -> v6.9.5
```
这里箭头指示的是默认的node版本，与下面查看当前node版本命令的结果一致。
```bash
$ nvm version
v6.9.5
```
这个时候可以使用默认的node版本，或者使用下面命令切换当前环境中的node版本。
```bash
$ nvm use --lts=argon
```
或者
```bash
$ nvm use v4.7.3
```

## 中国大陆配置淘宝的npm镜像
官方信息见[淘宝NPM镜像(TAONPM)](https://npm.taobao.org/)。你实际不太需要cnpm。

修改~/.npmrc文件，填写如下内容。没有就创建它。
```bash
# ~/.npmrc
registry=https://registry.npm.taobao.org
disturl=https://npm.taobao.org/mirrors/node
```

## 固定默认使用的版本
以上方法安装完成之后，尽管你可以用nvm use在不同的版本之间进行切换，但是下次重新进终端的时候选择的版本信息会丢失。

nvm会默认使用/.nvm/alias/default文件里指定的版本。
```bash
$ cat ~/.nvm/alias/default
lts/argon
```
但是，lts/argon或者lts/boron所指向的版本会随着时间指向不同的版本，可能现在指向v4.7.3，过一段时间可能会指向v4.7.4。

这会导致新版本发布之后，环境中找不到曾经安装过的node模块。

因为使用npm install -g安装的node模块的时候会在指定版本的node安装目录下安装，如~/.nvm/versions/node/v4.7.3/lib/node_modules/。

解决办法是创建/.nvmrc文件，内容如下。

```bash
# ~/.nvmrc
v4.7.3
```
这样默认版本就会固定为v4.7.3，并且nvm version命令的输出也相应着会变。

>多个版本下的node模块在不同的版本之间互相隔离安装的，比如v4.7.3下安装的模块，并不会自动出现在v6.9.5版本的模块目录中。

## 升级版本

官方文档指出可以使用如下命令迁移在老版本安装的node模块。
```bash
$ nvm install 6 --reinstall-packages-from=5命令
```

但是实践发现加了参数--reinstall-packages-from无法正确安装。

### 替代方案

1. 在安装新版本之前，或者安装新版本之后切回老版本。
   你可以在nvm ls结果中找到之前安装的版本。
2. 执行以下命令，可以检查全局安装的模块列表。
   ```bash
   $ npm list --global --depth=0 | grep -v 'npm'
   /Users/hanzhixing/.nvm/versions/node/v4.7.2/lib
   ├── fis3@3.4.32
   ├── hexo-cli@1.0.2
   ├── highlight.js@9.9.0
   ├── mockjs@1.0.1-beta3
   ├── tern@0.20.0
   └── vmd@1.28.0
   ```
3. 修改命令，让它变成单行模块名称序列。

   ```bash
   $ npm list --global --parseable --depth=0 | grep -v 'npm' | sed -n '1!p' | xargs basename | paste -s -d' ' -
   fis3 hexo-cli highlight.js mockjs tern vmd # 复制这个模块名列表
   ```
4. 切换到新版本，argon或者boron，并安装模块。
   ```bash
   $ nvm use --lts=argon
   $ npm install -g 这里粘贴上一步复制的模块名列表
   ```
   或者，使用下面这个命令一次性搞定，把其中的代号argon和版本号v4.7.2替换成你自己系统中对应的。
   ```bash
   $ nvm use --lts=argon >/dev/null 2>&1 && npm install -g `nvm use v4.7.2 >/dev/null 2>&1 && npm list --global --parseable --depth=0 | grep -v 'npm' | sed -n '1!p' | xargs basename | paste -s -d' ' -`
   ```
   这个命令执行完会切换到新版本。
5. 修改~/.nvmrc，固化新版本。
   ```bash
   $ nvm version > ~/.nvmrc
   ```

## 删除老版本
```bash
$ nvm uninstall v4.7.2
```
