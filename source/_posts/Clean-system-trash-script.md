---
title: 一键清理系统垃圾文件脚本(Clean system trash script) 
date: 2018-02-16 15:16:08
tags:
- script
- bat
categories:
- script
---

如题，是一个清理系统垃圾文件的批处理脚本的代码
运行会清除系统中的临时文件，如以下格式的临时文件
```
.tmp
._mp
.log
.gid
.chk
.old
.bak
……
```
以及以下目录的临时文件
```
%systemdrive%\recycled\
%windir%\prefetch\
%windir%\temp
%userprofile%\cookies\
%userprofile%\recent\
%userprofile%\Local Settings\Temporary Internet Files\
%userprofile%\Local Settings\Temp\
%userprofile%\recent\
……
```

- 对于缓解系统垃圾文件过多有一定用处
- 对于解决系统卡顿无较大作用

可以在关机前执行脚本清理系统的垃圾文件

看着哗啦啦清理的垃圾文件，心情舒畅

【本文仅为学习目的，如有侵权，请联系我删除】

<!-- more -->

## 使用方法

- 复制后文<脚本内容>中的全部内容
- 打开记事本（或其它文本编辑器，如Sublime Text、notepad、vim、写字板等）
- 新建空白文件（若默认新建空白文件，则不需要手动新建）
- 粘贴
{% asset_img 1.png %}
- 保存为.bat格式
{% asset_img 2.png %}
- 运行
{% asset_img 3.png %}
注：建议关机前（退出软件后）使用本脚本，否则会出现上图中某些临时文件因程序在运行拒绝访问而无法删除的情况；当然也可以不理会。

## 脚本内容

```
@echo off
@ ECHO.
@ ECHO.
@ ECHO. 说 明
@ ECHO -----------------------------------------------------------------------
@ ECHO 这是网上流传的批处理。它会帮您删除回收站、临时目录、最近打开过的文档痕迹
@ ECHO 等。对系统运行稍有帮助。如果您嫌电脑运行速度慢，不要指望用本批处理能搞好。
@ ECHO 电脑速度慢通常是因为太多无用的运算占据了CPU和内存资源所致。建议每隔一段
@ ECHO 时间，用Ghost恢复系统。这是最彻底的办法。
@ ECHO -----------------------------------------------------------------------
@ ECHO.

echo 正在清理系统垃圾文件，请稍等......
del /f /s /q %systemdrive%\*.tmp
del /f /s /q %systemdrive%\*._mp
del /f /s /q %systemdrive%\*.log
del /f /s /q %systemdrive%\*.gid
del /f /s /q %systemdrive%\*.chk
del /f /s /q %systemdrive%\*.old
del /f /s /q %systemdrive%\recycled\*.*
del /f /s /q %windir%\*.bak
del /f /s /q %windir%\prefetch\*.*
rd /s /q %windir%\temp & md %windir%\temp
del /f /q %userprofile%\cookies\*.*
del /f /q %userprofile%\recent\*.*
del /f /s /q "%userprofile%\Local Settings\Temporary Internet Files\*.*"
del /f /s /q "%userprofile%\Local Settings\Temp\*.*"
del /f /s /q "%userprofile%\recent\*.*"
echo 清理系统垃圾完成！
```