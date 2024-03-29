---
title: CTF 密码学（CTF Cryptography）
date: 2017-05-27 12:39:55
tags:
- CTF
- 密码
categories:
- CTF
---
# 密码学 Cryptography
（在西欧语文中，源于希腊语kryptós“隐藏的”，和gráphein“书写”）
是研究如何隐密地传递信息的学科（其实就是研究编制密码和破译密码的技术科学）
在现代特别指对信息以及其传输的数学性研究，常被认为是数学和计算机科学的分支，此外和信息论也密切相关
编制密码是为了安全传递信息，因此密码学也是信息安全不可或缺的一个部分
在CTF中，密码学也占着相当重要的地位
因此，多学习一些密码的相关知识，不管对于密码学的理解还是日常生活都有很大的帮助哦~
今天就讲讲CTF中有关密码的一些知识和常见的不常见的密码编制和破译方式

【本文仅为学习目的，如有侵权，请联系我删除】
<!-- more -->

## 0x00 MD5【Message Digest Algorithm 5（信息摘要算法 5）】
## 0x00 SHA1【Secure Hash Algorithm（安全哈希算法）】（SHA类同MD5）

其实严格意式义上MD5并不能算一种密码编制方式，而是信息摘要算法
（这也是放在0x00的原因）
众所周知，地球上任何人都有自己独一无二的指纹，与之类似
MD5可以为任何文件（不管其大小、格式、数量）产生一个同样独一无二的“数字指纹”
如果任何人对文件名做了任何改动，其MD5值也就是对应的“数字指纹”都会发生变化
因此依靠MD5算法产生的MD5值就可以识别文件是否被篡改

MD5算法具有以下特点：
1、压缩性：任意长度的数据，算出的MD5值长度都是固定的（常见的有32位和16位）
2、容易计算：从原数据计算出MD5值很容易
3、抗修改性：对原数据进行任何改动，哪怕只修改1个字节，所得到的MD5值都有很大区别
4、强抗碰撞：已知原数据和其MD5值，想找到一个具有相同MD5值的数据（即伪造数据）是非常困难的
（但不代表就不存在不同数据相同MD5值的情况）

在CTF中，更多的情况是使用MD5来加密字符串，比如：
明文：I LOVE A&D
密文：c8a48a4e88aafe4396bbebe273a802e9

MD5算法还有许多的变种，这里就不详细介绍，有兴趣可以自行了解~
相关网址推荐：www.cmd5.com

## 0x01 Base64

Base64是网络上最常见的用于传输8Bit字节代码的编码方式之一，也是最常见的二进制编码方法之一
Base64是一种用64个字符来表示任意二进制数据的方法（名字由来）
Base64编码表见下
{% asset_img Base64-1.png Base64-1 %}
Base64的原理很简单，首先需要准备好上图中的编码表
然后，对二进制数据进行处理，每3个字节一组
一共是3x8=24bit，划为4组，每组正好6个bit
{% asset_img Base64-2.png Base64-2 %}
比如上图中b1、b2、b3一共3个字节，划分后变为n1、n2、n3、n4一共4组
即得到4个数字作为索引，然后查表，获得相应的4个字符，就是编码后的字符串
所以，Base64编码会把3字节的二进制数据编码为4字节的文本数据，长度增加33%
如果要编码的二进制数据不是3的倍数，Base64会自动用\x00字节在末尾补足后
再在编码的末尾加上1个或2个=号，表示补了多少字节，解码的时候，=号则会自动去掉

使用Base64加密字符串，比如：
明文：I LOVE A&D
密文：SSBMT1ZFIEEmRA==

相关网址推荐：base64.xpcha.com

## 0x02 栅栏密码

栅栏密码，就是把要加密的明文分成N个一组，然后把每组对应的字符依次取出连接成新的一组字符串
这样就可以得到N组新的字符串，再依次把N组字符串连接，形成一段无规律的话
（觉得难以理解可以看下面举例）

一般比较常见的是2栏的栅栏密码。比如：
明文：I LOVE A&D
去掉空格后变为：ILOVEA&D，一共8个字符，
可以2个一组分组，即  IL OV EA &D
先取出第一个字母：IOE&
再取出第二个字母：LVAD
连在一起就是：IOE&LVAD
即密文为IOE&LVAD，还原密文的过程即为加密的逆过程
可以先把密文从中间分开，变为两行：
I  O  E  &
L  V  A  D
再按上下上下的顺序组合起来：
ILOVEA&D
分出空格，就可以得到原文了：
I LOVE A&D

当然，不是所有密码都分为两栏，可以多尝试不同的分组解密，当语意符合时一般即为正确原文


## 0x03 凯撒（Caesar）密码
## 0x03 维吉尼亚（Virginia）密码

凯撒密码是一种代换密码。据说凯撒是率先使用加密函的古代将领之一，因此这种加密方法被称为恺撒密码
凯撒密码是一种古老的对称加密体制，基本思想是：通过把字母移动一定的位数来实现加密和解密
明文中的所有字母都在字母表上向后（或向前）按照一个固定数目进行偏移后被替换成密文
例如，当偏移量是3的时候，所有的字母A将被替换成D，B变成E，以此类推X将变成A，Y变成B，Z变成C
但是和所有的利用字母表进行替换的加密技术一样
恺撒密码非常容易被破解（穷举破解），因此在实际应用中也无法保证通信安全

使用凯撒密码加密字符串，比如：
明文：I LOVE A D
偏移量：1
密文：J MPWF B E

单一恺撒密码太过于简单，因此人们在此基础上扩展出多表密码，称为“维吉尼亚”密码
维吉尼亚密码将26个恺撒密表合成一个，如下：
{% asset_img Caesar-1.png Caesar-1 %}
然后引入“密钥”的概念，即根据密钥来决定用哪一行的密表来进行替换
假如以上图第一行代表明文字母，左面第一列代表密钥字母，对如下明文加密：
明文：I LOVE A D
密匙：I LOVE A D（密匙和明文不一定一样，最好不一致，这里只是为了方便取一致）
密文：Q WCQI A G

维吉尼亚密码相对单一恺撒密码加密程度提高了很多
历史上以维吉尼亚密表为基础又演变出很多种加密方法
其基本元素无非是密表与密钥，并一直沿用到二战以后的初级电子密码机上


## 0x04 摩斯（Morse）密码

摩尔斯电码（又译为摩斯密码，Morse code）是一种时通时断的信号代码
通过不同的排列顺序来表达不同的英文字母、数字和标点符号
它发明于1837年，是一种早期的数字化通信形式
但是它不同于现代只使用零和一两种状态的二进制代码
它的代码包括五种： 
- 点
- 划
- 点和划之间的停顿
- 每个字符间短的停顿（在点和划之间）
- 每个词之间中等的停顿以及句子之间长的停顿

知识扩展：SOS是国际通用求救信号，这三个字母并非任何单词缩写
只是因为它的电码“ ···---···”（三点，三长，三点）是电报中最容易发出和辨识的电码

国际摩斯密码对照表：
{% asset_img Morse-1.png Morse-1 %}
{% asset_img Morse-2.png Morse-2 %}
使用摩斯密码加密字符串，比如：
明文：I LOVE A D
密文：·· ·-·· --- ···- · ·- -··

## 0x05 培根（Bacon）密码

培根所用的密码是一种本质上用二进制数设计的，没有用通常的0和1来表示，而是采用a和b

培根密码加密原理：
培根密码其实加密原理也很简单，就是利用假信息，来表示明文中的内容
这里的a和b并不是严格意义上字母表中的a和b，而是表示两个字符的不同
（可以是字体不同，有加粗和不加粗，斜体和非斜体，有下划线和没有下划线）
只要是不同都可以，不拘泥于形式
简单的讲就是用假信息中5个字符一组，对照密码表修改字符的格式
每组表示明文中的一个字符，如果使用的好，将完全看不出有加密，混淆度比较高

使用培根密码加密字符串，比如：
明文：I LOVE A D
去掉空格：ILOVEAD
对照密码表：abaaa ababb abbba babab aabaa aaaaa aaabb
假信息：woaiADwoaiADwoaiADwoaiADwoaiADwoaiA（假信息是自己选择的）
5个字符一组：woaiA  Dwoai  ADwoa  iADwo   aiADw  oaiAD  woaiA
根据对照密码表改变每组字符的格式，得到密文
密文：WoAIA  DwOai  AdwoA  iAdWo   AIaDW  OAIAD  WOAia
（这里用大写表示A，小写表示b）

培根密码表：
{% asset_img Bacon-1.png Bacon-1 %}

## 0x06 猪圈（Pig）密码

猪圈密码（亦称朱高密码、共济会暗号、共济会密码或共济会员密码）
是一种以格子为基础的简单替代式密码
猪圈密码其实只是一种外形古怪的密码，加密的级别非常低，为幼儿级水平，易破解
以下是常见的猪圈密码对照表：
{% asset_img Pig-1.png Pig-1 %}
{% asset_img Pig-2.png Pig-2 %}
使用猪圈密码加密字符串，比如：
明文：I LOVE A D
密文：
{% asset_img Pig-3.png Pig-3 %}

正经的加密讲完了，接下来是一些比较特别~的加密方式
因此也不需要了解加密方式，当然，感兴趣的同学可以自行了解~


## 0x07 Brainfuck/Ook!

明文：I LOVE A&D
密文（Brainfuck）：
```
+++++ +++[- >++++ ++++< ]>+++ +++++ +.<++ ++++[ ->--- ---<] >---- -.<++
++++[ ->+++ +++<] >++++ ++++. +++.+ +++++ +.<++ ++[-> ----< ]>-.< +++++
+[->- ----- <]>-. <++++ +[->+ ++++< ]>+++ +++++ .<+++ ++[-> ----- <]>--
.<+++ ++[-> +++++ <]>++ +++.< 
```
密文（Ook!）：
```
..... ..... ..... .!?!! .?... ..... ..... ...?. ?!.?. ..... ..... .....
..!.? ..... ..... ...!? !!.?! !!!!! !!!!! !?.?! .?!!! !!!!! !!!.? .....
..... ...!? !!.?. ..... ..... .?.?! .?... ..... ..... ...!. ..... .!...
..... ..... ..!.? ..... ....! ?!!.? !!!!! !!!?. ?!.?! !!.?. ..... .....
..!?! !.?!! !!!!! !!!!! ?.?!. ?!!!. ?.... ..... ..!?! !.?.. ..... ...?.
?!.?. ..... ..... ..... !.?.. ..... ....! ?!!.? !!!!! !!!!! ?.?!. ?!!!!
!.?.. ..... ....! ?!!.? ..... ..... ?.?!. ?.... ..... .!.?. 
```
相关网址推荐：www.splitbrain.org/services/ook

## 0x08 jsfuck
一般用来加密JavaScript，密文只包含		()[]{}!+		等字符，因此也比较容易辨认

明文：alert(1)
密文：
```
[][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+!+[]]][([][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+!+[]]]+[])[!+[]+!+[]+!+[]]+(!![]+[][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+!+[]]])[+!+[]+[+[]]]+([][[]]+[])[+!+[]]+(![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[])[+!+[]]+([][[]]+[])[+[]]+([][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+!+[]]]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+!+[]]])[+!+[]+[+[]]]+(!![]+[])[+!+[]]]((![]+[])[+!+[]]+(![]+[])[!+[]+!+[]]+(!![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+!+[]]+(!![]+[])[+[]]+(![]+[][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+!+[]]])[!+[]+!+[]+[+[]]]+[+!+[]]+(!![]+[][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+!+[]]])[!+[]+!+[]+[+[]]])()
```
相关网址推荐：
www.jsfuck.com
patriciopalladino.com/files/hieroglyphy/

## 0x09 XXencode/UUencode

明文：I LOVE A&D
密文（XXencode）：8GG-AHpN3622aF+++
密文（UUencode）：*22!,3U9%($$F1```

相关网址推荐：web.chacuo.net/charsetxxencode

## 0x10 手机键盘加密

首先，这里的手机键盘加密里的手机键盘一般指的是比较常见的九宫格布局， 见上图
然后手机键盘加密方式的原理也比较简单：把字符转换为手机的按键
例如：字符a变为21，r变为73，z变为94
因此，我们也可以知道手机键盘加密方式不可能用1开头，第二位数字不可能超过4
{% asset_img Mobile-1.png Mobile-1 %}
另外，利用重复的数字代表字母也是可以的，例如：
a可以用21表示，也可以用2表示
Z可以用94表示，也可以用9999表示
这里也说明，重复的数字最小为1位，最大为4位

明文：I LOVE A D
密文1：43 53638332 21 31
密文2：444 555666688833 22 3

## 0x11 （电脑）键盘加密

{% asset_img pc-1.png pc-1 %}
电脑键盘加密类似手机键盘加密，一般指的是全键盘布局（好像是废话），见上图
电脑键盘加密有两种方式，都比较简单：
①用字符把字符“围”起来
例如：字符s需要用字符awedxz围起来，因此可以用awedxz表示s；用gyujnb表示h；等等
②用字符吧字符“画”出来
例如：awedxz在键盘上的形状为“o”，因此可以用awedxz表示o；等等


## 参考资料

非常感谢以下列出的与未列出的各篇文章与资料，本文内容为学习了以下资料的产物
再次表示忠心的感谢

本文中也引用了以下资料中的部分图片、文字等
【本文仅为学习目的，如有侵权，请联系我删除】

  1. [md5——百度百科](http://baike.baidu.com/item/MD5)
  2. [base64——廖雪峰](http://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/001431954588961d6b6f51000ca4279a3415ce14ed9d709000)
  3. [栅栏密码——百度百科](http://baike.baidu.com/item/%E6%A0%85%E6%A0%8F%E5%AF%86%E7%A0%81)
  4. [恺撒密码——百度百科](http://baike.baidu.com/item/%E6%81%BA%E6%92%92%E5%AF%86%E7%A0%81)
  5. [维吉尼亚密码——百度百科](http://baike.baidu.com/item/%E7%BB%B4%E5%90%89%E5%B0%BC%E4%BA%9A%E5%AF%86%E7%A0%81)
  6. [摩斯密码——百度百科](http://baike.baidu.com/item/%E6%91%A9%E5%B0%94%E6%96%AF%E7%94%B5%E7%A0%81)
  7. [培根密码——百度百科](http://baike.baidu.com/item/%E5%9F%B9%E6%A0%B9%E5%AF%86%E7%A0%81)
  8. [猪圈密码——百度百科](http://baike.baidu.com/item/%E7%8C%AA%E5%9C%88%E5%AF%86%E7%A0%81)
  9. [那些年做过的ctf之加密篇——乌云](http://drops.wooyun.org/tips/10002)

  最后更新时间：2017年5月28日 23:50:53