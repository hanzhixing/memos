[返回首页](../README.md)

# 正则表达式

## 引擎
不同的计算机语言，不同的工具或者环境中实现的解释引擎不完全一致。 但有两种标杆算法。

## 算法
算法有DFA和NFA两种。可以看CSDN上的[NFA/DFA算法](http://blog.csdn.net/yukuninfoaxiom/article/details/6057736)。

## 标准
因为解释引擎实现有差异，标准也有很多种。

[BRE](https://en.wikipedia.org/wiki/Regular_expression#POSIX_basic_and_extended)和[ERE](https://en.wikipedia.org/wiki/Regular_expression#POSIX_extended)在Linux的很多命令中都用到。

[PCRE](http://www.pcre.org/current/doc/html/)也基本是必装的库，像Nginx或者PHP都是用的这个标准。

## 场景
- grep默认使用BRE，加上参数可以使用ERE和PCRE。
- find默认使用Emacs的标准。加上参数可以使用awk，BRE，ERE。
- awk使用ERE，但有点不一样。
- sed默认使用BRE，但有点不一样。加上参数可以使用ERE。
- Vim使用自己的标准。
- Emacs使用自己的标准。介于BRE和ERE。
- PHP官方只推荐PCRE。
- NginX是使用PCRE。
