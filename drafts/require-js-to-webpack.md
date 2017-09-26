[返回首页](../README.md)

# 从Require.js到Webpack的迁移

## Require.js的配置迁移
### path
我们都知道它是模块id到模块寻找路径的映射关系。它们分三种类型：

1. npm或bower安装的模块。
2. 不属于app，但app里要使用的第三方模块。
3. 属于app，自己实现的模块。

对于第一种，我们的目的是迁移到webpack，所以首先考虑把模块的安装方式从bower切换到npm。
bower的定位是浏览器端使用的模块的包管理器。
但grunt、gulp、webpack等主流的构建工具都是有办法自己压缩js、css等静态资源，并不需要使用bower这种专设的包管理器来得到压缩之后的静态资源版本。
再则如果是浏览器端使用的包，即使是npm方式安装的，大多数也都提供压缩版本的静态资源，并且它们的package.json种的main入口也很多都是直接指向压缩版本。
