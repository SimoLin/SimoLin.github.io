---
title: Java JDK 配置环境变量(environment variables configuration)
date: 2018-02-14 20:22:07
tags:
- Java
categories:
- Java
---

重装系统之类的时候，
需要重新安装Java、配置环境变量，
每次都自己去找太麻烦了，
想想干脆做个备忘，
需要自取~

注：为了Markdown表格显示的美观和照顾Windows10系统开始添加或修改环境变量时(特别是Path变量)需要单个单个变量值添加，将Path和CLASSPATH变量每一个变量值都单独分行。若你不符合上述情况，将变量所有的变量值连起来即可，或参照第二个表格(首页不显示，请单击进入文章阅读更多)。

变量名    | 变量值
----------|-------------------------------------------
JAVA_HOME |C:\Program Files\Java\jdk1.8.0_131<修改为自己的JDK安装地址>
Path      |;%JAVA_HOME%\bin;
		  |%JAVA_HOME%\jre\bin;
CLASSPATH |.;%JAVA_HOME%\lib;
		  |%JAVA_HOME%\lib\dt.jar;
		  |%JAVA_HOME%\lib\tools.jar;

【本文仅为学习目的，如有侵权，请联系我删除】
<!-- more -->

两个表格内容一致，第二个表格未进行分行，方便旧操作系统添加环境变量。

变量名    | 变量值
----------|-------------------------------------------
JAVA_HOME |C:\Program Files\Java\jdk1.8.0_131<修改为自己的JDK安装地址>
Path      |;%JAVA_HOME%\bin;%JAVA_HOME%\jre\bin;
CLASSPATH |.;%JAVA_HOME%\lib;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar;


最后更新时间：2018年2月16日 15:46:32