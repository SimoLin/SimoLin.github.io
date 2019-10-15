---
title: Java JDK 配置环境变量(environment variables configuration)
date: 2018-02-14 20:22:07
tags:
- Java
categories:
- Java
---


重新安装Java，配置环境变量，
每次都去找正确的教程太麻烦，
干脆自己做个教程记录，
需要自取

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

注：为了Markdown表格显示的美观和照顾Windows10系统开始添加或修改环境变量时(特别是Path变量)需要单个单个变量值添加，将Path和CLASSPATH变量每一个变量值都单独分行。

若你不符合上述情况，将变量所有的变量值连起来即可，或参照第二个表格。

两个表格内容一致，第二个表格未进行分行，方便旧操作系统添加环境变量。

# Linux

直接下载对应版本的包，如jdk-8u131-linux-x64.tar.gz
解压到任意你觉得好的目录，如/home/simo/jdk1.8.0_131

使用以下命令添加环境变量
```
sudo vim ~/.bashrc
```
在文件的末尾追加下面内容:
```
#set oracle jdk environment
export JAVA_HOME=/home/simo/jdk1.8.0_131  # 这里要注意目录要换成自己解压的jdk目录
export JRE_HOME=${JAVA_HOME}/jre  
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib  
export PATH=${JAVA_HOME}/bin:$PATH  
```
使环境变量马上生效
```
source ~/.bashrc
```


# 更新历史
## 2019-01-02 17:20:10
增加Linux配置方法
## 2019-10-09 15:08:26
文字调整