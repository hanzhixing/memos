[返回首页](../README.md)

# 多版本Java共存
>解决环境中使用多个Java版本的问题。

使用[jenv](https://github.com/gcuisinier/jenv)。

```bash
# ~/.profile (in Debian)
PATH="$HOME/.jenv/bin:$PATH"
eval "$(jenv init -)"
```

安装和使用都非常简单。看官方说明。

# JAVA_HOME

JAVA_HOME应尽量避免在.bashrc或者.profile文件中配置。
尤其是支持多个Java版本的构建系统中更是如此。

[如何给Tomcat配置JAVA_HOME和JAVA_OPTS](./tomcat-vhost.md)

