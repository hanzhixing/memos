[返回首页](../README.md)

# Doom Emacs

[Doom Emacs](https://github.com/doomemacs/doomemacs)的官方文档还不那么完善。

不少知识是找起来比较困难的。

有的在Github Issue里，有的在Discord，有的在源码里。

把辅助回忆的和比较容易忘记的记录在这里，防止重复查找资料。

## Emacs的安装
Doom Emacs在官方文档中推荐使用`brew`安装`emacs-mac`。

除了官方文档中推荐的`--with-modules`参数，还需要增加`--with-starter`。

这样`Terminal`中执行`emacs`的时候才会去执行GUI版本的emacs，得到和双击打开`/Applications/Emacs.app`时一样的效果。

看上去`emacs-mac`的29版本开始`--with-modules`是默认参数，并没有提供显式的参数。

## Chemacs2
Doom Emacs暂时无法通过[Chemacs2](https://github.com/plexus/chemacs2)来加载。

见[https://github.com/doomemacs/doomemacs/issues/5845](https://github.com/doomemacs/doomemacs/issues/5845)。

Emacs 29增加了新的指定初始化目录的参数`–init-directory`，将来可能不太需要使用Chemacs2。

等Doom Emacs重写CLI之后再看看。

## 特殊文件

### `.project `

[core-projects.el](https://github.com/doomemacs/doomemacs/blob/develop/core/core-projects.el#L53-L69)文件中可以知道Doom Emacs是如何识别项目根目录的。

### `.ignore`

通过ivy搜索文件时如果存在.gitignore，会根据.gitignore文件内容来排除搜索目录，否则会去查找.ignore文件。

## 查找文件，Buffer

### `/`
在当前Buffer（文件中）中搜索。用`n`和`N`可以正反方向跳转。

### `SPC .`
文件系统中查找文件。

### `SPC SPC`
当前项目中查找文件。

### `SPC p r`
当前项目中最近打开过的文件。

## 搜索文本

### `SPC s p`
在当前项目中搜索文本。

### `C-c C-e`
搜索到的匹配内容及文件之后，编辑所有（跨文件，多个文件）。

## 快速定位跳转
因为是vim键位，在不用鼠标的情况下，想快速把光标移动到屏幕中的某个字符，就需要下面的方法。

### `s` + 2个字符（当前行）

功能和vim的`f`和`F`跳转类似，只不过不是输入一个字符，而是输入2个连续的字符。

输入`s`或`S`之后会在输出栏显示`2>`，接着输入2个字符即可。

该跳转方法是通过[evil-snipe](https://github.com/hlissner/evil-snipe)实现的。

`s`对应vim的`f`，`S`对应vim的`F`。

输入完成时，是在当前行中查找字符。但是如果输入`;`（正向）或`,`（反向），会在整个Buffer中跳转。

### `g s SPC` + 2个字符（可视区域）

输入`g s SPC`之后快速输入你眼睛正盯着的相邻的两个字符，如果可视区域只存在一处，则会直接跳转到目标位置。

否则会在所有出现的位置显示英文字符，输入这些字符就会跳转到对应的位置。

显示代表位置的英文字符的时候，会替换所有目标位置的内容，但这种显示只是临时的，完成跳转之后就会消失。

该跳转方法是通过[avy](https://github.com/abo-abo/avy)实现的。

如果在Doom Emacs的配置文件`config.el`中配置了`(setq avy-all-window t)`，则可以跨窗口进行跳转。

### 其他说明
输入`v`进入`VISUAL`模式下，以上两个方法都可以使用，用来选择文本。

在写程序代码的时候还有一个经常使用的功能就是[evil-surround](https://github.com/emacs-evil/evil-surround)。但是它也需要通过`s`作为指令。这个时候会和`evil-snipe`的`s`发生冲突。

这个时候`evil-snipe`的`s`和`S`需要替换成`z`和`Z`。

在`VISUAL`模式下，我们有的时候并不希望目标字符也被选中，这个时候需要用`x`和`X`来代替。


## 多点编辑（多个光标）
Doom Emacs通过[evil-multiedit](https://github.com/hlissner/evil-multiedit)和[evil-mc](https://github.com/gabesoft/evil-mc)两种实现来提供同时编辑多处文本的功能。

整体上，`evil-multiedit`会更容易使用，但是功能没有`evil-mc`强大。

### evil-multiedit

在`VISUAL`模式下选中文本，再输入`R`，就会选中所有匹配的文本。

这时，可以通过`C-n`（正向）和`C-p`（反向）移动光标，按`RET`（回车键）可以取消选中光标所在处的匹配项。

输入`I`或`A`进入`INPUT`模式，就可以根据需要同时修改选中的所有内容。

`ESC`或`C-[`来退出多点编辑模式。

### evil-mc

`g z m` 在Buffer中所有与光标位置对象相同的匹配项所在处插入虚拟光标。

`g z d` 在光标所在位置新建虚拟光标，并移动到下一个匹配处。`g z D`会反向查找匹配向。

`g z j` 在光标所在位置新建虚拟光标，并移动到下一行。`g z k`反向移动光标。

`g z z` 在光标所在处新建虚拟光标。

`g z u` 移除所有虚拟光标。

在已经有虚拟光标创建出来的时候，通过`g z t`可以临时关闭虚拟光标跟随真实光标移动的功能，跟随移动的状态下通过`g z z`创建新的虚拟光标会有等同效果。如果想恢复跟随移动，再按一次`g z t`即可。

## 撤销/重做
和vim一样通过`u`来撤销，`C-r`来重做。底层使用Emacs中更强大的`undo-tree`系统。

## 代码跳转
`g d`或`SPC c d`来跳转到定义处。

`g D`或`SPC c D`来列出所有引用。

`K`或`SPC c k`来进行外部搜索（Google或Wikepedia等）。

## Narrowing和Widening
两个功能都是Emacs特有的编辑方式。有些时候，当我们进行文本替换时，我们只希望把一部分文本块中的特定内容替换成其他的。这个时候Narrowing和Widening功能就比较有用。

`SPC b -`来`Narrow`，再按一次来`Widen`(退出Narrow模式)。

## 其他
`g c c` 注释或取消注释
`SPC x` 打开`scratch`Buffer

## 源码
[keybindings](https://github.com/doomemacs/doomemacs/blob/develop/modules/config/default/+evil-bindings.el)中可以查看到内置的快捷键设置。
