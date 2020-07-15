---
title: Java JDK 配置环境变量(Java JDK environment variables configuration)
date: 2018-02-14 20:22:07
tags:
- 教程
categories:
- 教程
---

记录Java JDK环境变量信息

【本文仅为学习目的，如有侵权，请联系我删除】
<!-- more -->

# Windows

## Windows 10

变量名    | 变量值
----------|-------------------------------------------
JAVA_HOME |C:\Program Files\Java\jdk1.8.0_131【修改为自己的JDK安装地址】
Path      |%JAVA_HOME%\bin
		  |%JAVA_HOME%\jre\bin
CLASSPATH |.;%JAVA_HOME%\lib;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar;

## Windows 7

变量名    | 变量值
----------|-------------------------------------------
JAVA_HOME |C:\Program Files\Java\jdk1.8.0_131【修改为自己的JDK安装地址】
Path      |;%JAVA_HOME%\bin;%JAVA_HOME%\jre\bin;
CLASSPATH |.;%JAVA_HOME%\lib;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar;

> 注：两个表格内容一致，第二个表格未进行分行，方便根据系统情况复制。

# Linux

直接下载对应版本的安装包，如
```
jdk-8u131-linux-x64.tar.gz
```

解压到安装目录，如

```
/home/[用户名]/jdk1.8.0_131
```

使用以下命令添加环境变量

```
sudo vim ~/.bashrc
```

在文件的末尾追加下面内容:

```
#set oracle jdk environment
export JAVA_HOME=/home/[用户名]/jdk1.8.0_131  # 这里要注意目录要换成自己解压的jdk目录
export JRE_HOME=${JAVA_HOME}/jre  
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib  
export PATH=${JAVA_HOME}/bin:$PATH  
```

使环境变量马上生效

```
source ~/.bashrc
```