[返回首页](../README.md)

# Tomcat的虚拟主机

以下内容仅适用于自己下载解压安装的Tomcat，不包含使用yum、apt等软件包管理器安装的。

## 环境变量在bin/setenv.sh文件中管理

打开tomcat的bin目录下的catalina.sh文件，可以看到以下内容。

```bash
# -----------------------------------------------------------------------------
# Control Script for the CATALINA Server
#
# Environment Variable Prerequisites
#
#   Do not set the variables in this script. Instead put them into a script
#   setenv.sh in CATALINA_BASE/bin to keep your customizations separate.
#
#   CATALINA_HOME   May point at your Catalina "build" directory.
```

根据提示，在tomcat的bin目录下添加setenv.sh文件。

```bash
# bin/setenv.sh
#!/bin/bash

export JAVA_HOME="$(jenv javahome)" # 用了jenv动态获取当前JDK安装目录。可以写绝对路径。
export JAVA_OPTS="-Xmx2048m -Xms1024m -XX:PermSize=128m -XX:MaxPermSize=256m -Dfile.encoding=UTF-8"
```

## 虚拟机主机配置在conf/server.xml

```xml
<!-- conf/server.xml -->

  <!-- ... -->

  <Engine name="Catalina" defaultHost="localhost">

    <!-- ... -->

    <!-- 添加以下内容 -->
    <Host
        <!-- 域名不需要写端口 -->
        name="mydomain.com"
        <!-- 编译产出目录的绝对路径 -->
        appBase="/path/to/the/build/directory/of/your/java/project"
        unpackWARs="true"
        autoDeploy="true">

        <!-- 至少在Tomcat7，path="" 和 docBase=""不能少 -->
        <Context path="" docBase="/path/to/the/build/directory/of/your/java/project" crossContext="false" reloadable="true" />

        <!-- ... -->

    </Host>

    <!-- ... -->

  </Engine>

  <!-- ... -->

```
