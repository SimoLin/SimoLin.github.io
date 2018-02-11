---
title: CTF 介绍以及相关学习资源（CTF Introduction）
date: 2017-05-27 11:21:26
tags:
- CTF
categories:
- CTF
---
# CTF（Capture The Flag）

中文一般译作**夺旗赛**，在网络安全领域中指的是网络安全技术人员之间进行技术竞技的一种比赛形式
CTF起源于1996年DEFCON全球黑客大会，以代替之前黑客们通过互相发起真实攻击进行技术比拼的方式
发展至今，已经成为全球范围网络安全圈流行的竞赛形式
比赛的大致流程是，参赛团队通过完成题目、或者队伍之间进行网络攻防等方式
率先从主办方给出的题目或比赛环境中**得到一串具有一定格式的字符串**或其他内容，并将其提交给主办方，从而夺得分数
> 这样的内容称为“Flag”，如：flag_A&D{I_LOVE_A&D}

<!-- more -->

## CTF竞赛模式
具体可分为以下三类：

### 解题模式（Jeopardy）
在解题模式CTF赛制中，参赛队伍可以通过互联网或者现场网络参与，以解决网络安全技术挑战题目的分值和时间来排名
题目主要包含逆向、漏洞挖掘与利用、Web渗透、密码、取证、隐写、安全编程等类别

### 攻防模式（Attack-Defense）
在攻防模式CTF赛制中，参赛队伍在网络空间互相进行攻击和防守
挖掘网络服务漏洞并攻击对手服务来得分，修补自身服务漏洞进行防御来避免丢分

### 混合模式（Mix）
结合了解题模式与攻防模式的CTF赛制，比如参赛队伍通过解题可以获取一些初始分数
然后通过攻防对抗进行得分增减的零和游戏，最终以得分高低分出胜负

## CTF比赛主要题型
  1. Misc（杂项，即结合多种下述类型的题目）
  2. Reverse（逆向分析）
  3. Crypto（密码、加密解密、安全编程）
  4. Mobile（apk，安卓，ios）
  5. Exploit（漏洞利用）
  6. Web（Web应用的漏洞挖掘和利用）
  7. Recon（社工、情报搜集分析）
  8. Forensic（网络流量分析、隐写分析、系统取证）

可以根据自己的兴趣选择合适的方向入门

接下来就以每个方向举一道例题（题目类型和做题思路）来做一个简单的介绍：
注：
①Misc为杂项（多个方向合题），所以不单独举例
②举例只讲题目内容和解题思路，不讲具体解题方式！！因为不同题目解题方式不同

## 题型简介

### Reverse（逆向分析）

{% asset_img Reverse-1.png Reverse-1 %}
如上图为  2015广州强网杯  原题 KeyGenMe 150分
题目文件格式：.exe
题目提示：用户名：gdqwb
题目目标：找一个可用注册码SN，即图中pass

一般解题思路：
先用查壳软件（如：ExeinfoPe）查壳，有壳先脱壳。（不懂可以先留着没关系）
然后根据程序不同丢进不同软件进行分析，一般用OD、IDA等
然后就是分析程序代码了~！（一般是汇编语言，如下图）
{% asset_img Reverse-2.png Reverse-2 %}
具体分析过程就不讲了，主要是了解解题过程~
了解程序是如何判断pass之后就可以得到pass，得到本题flag

### Crypto（密码、加密解密、安全编程）

{% asset_img Crypto-1.png Crypto-1 %}
如上图为  2015广州强网杯  原题 正确的密码 50分
题目提示：4e8f794089bl6b4ef55cd0399dca1433c
题目目标：找出原密码

没接触过CTF的童鞋可能看到这道题目会有点懵，其实这道题比较简单，毕竟分数只有50分
在了解了常见的加密方式（[可见于博客里另一篇关于CTF密码学的文章](http://simolin.cn/2017/05/27/CTF-Cryptography/)）之后
可以一眼看出4e8f794089bl6b4ef55cd0399dca1433c该字符串为md5加密
但如果直接拿去md5解密会发现有错误，因为很明显，该字符串不符合md5的要求
md5一般为16位或32位，而该字符串为33位
{% asset_img Crypto-2.png Crypto-2 %}
而到底是多出了哪一个字符？如果一个一个试就比较麻烦了
事实上，md5加密后不会出现l字符，所以l字符是多余的
去掉l字符后，字符串变为4e8f794089b6b4ef55cd0399dca1433c
解密后得到：huang，即为该题flag
{% asset_img Crypto-3.png Crypto-3 %}

### Mobile（apk，安卓，ios）

{% asset_img Android-1.png Android-1 %}
如上图为  2015广州强网杯  原题 初露锋芒 200分 
题目文件格式：.apk（安卓app格式）
题目提示：biu！Biu！Biu！
题目目标：找到name和pass

一般解题思路：
该题类似前面讲过的Windows逆向题目，其实这是一道安卓逆向题目，所以目标和过程都是类似的
通过分析该程序的代码，逆向得到正确的name和pass，得到flag
但是具体的解题方式和Windows逆向题还是不太一样的，首先改变.apk为.rar
（apk本质上和rar类似，感兴趣可以自行了解），解压得到主程序文件classes.dex
{% asset_img Android-2.png Android-2 %}
接着利用工具dex2jar将classes.dex转换为jar格式
{% asset_img Android-3.png Android-3 %}
再用软件jd-gui-windows-1.4.0打开，即可看到软件的反编译代码
{% asset_img Android-4.png Android-4 %}
然后就是分析程序代码了~！（逆向题一般都是分析代码）
得到找到name和pass即可获得flag

### Exploit（漏洞利用）

漏洞利用题目类似Windows逆向，一般会给一个服务器的地址，该服务器上运行着一个程序
题目一般会给出该程序的源代码，让选手去研究分析程序，找出程序的漏洞
过程类似Windows逆向，但更考研对堆栈的操作（常见的漏洞有缓冲区溢出、格式化漏洞等）
这里就不具体举例，主要还是分析代码

### Web（Web应用的漏洞挖掘和利用）

web题目考点多，有XSS、SQL注入、任意文件上传、CSRF、ssrf等等
有时候也会考验选手的脑洞，有难的也有简单的题目
{% asset_img Web-1.png Web-1 %}
如上图为  2015广州强网杯  原题 远程管理系统 100分
题目目标：找出flag
打开题目网址后，如下图所示：
{% asset_img Web-2.png Web-2 %}
如图可见是一个“远程管理系统”，依正常的思维就必须获取正确的帐号密码登录进去
这里忽略如何找到正确的帐号密码过程(使用or 1 = 1 '绕过验证)，正确登录后如下图：
发现是控制台，利用命令找到flag文件打开得到flag
{% asset_img Web-3.png Web-3 %}
Web类题目就是从Web应用中（用各种方法）获取flag。

### Recon（社工、情报搜集分析）

{% asset_img Recon-1.png Recon-1 %}
如上图为  2015广州强网杯  原题 女神在哪儿 500分
题目提示：压缩包中有和女神的一些聊天记录，1张手机截图，和2张十分模糊的实地照片
题目目标：找出女神的地址（即为flag）
{% asset_img Recon-2.png Recon-2 %}
题目的素材里给的线索有：
温州机场附近十几公里、有山有湖、天气、酒店图、手机截图、2张模糊的照片
简单讲就是要从这些线索里找到女神的地址……
社工题一般脑洞挺大的，这道题也差不多，至少小编我当时比赛的时候没做出来

具体分析过程就不写了，社工题目基本就是这样了~

### Forensic（网络流量分析、隐写分析、系统取证）
{% asset_img Forensic-1.png Forensic-1 %}
一般流量分析题目使用的软件是wireshark，如图：
通过分析网络流量中数据包的内容，找出需要的信息，得到flag

## 其它
简单介绍完了各个方向的题目解题过程，你有对哪个方向产生兴趣吗？
是否也想投入CTF比赛中来呢？

### 推荐一些有关CTF的文章：
对于哪个方向感兴趣可以自行了解更详细的内容~

[如何开始你的CTF比赛之旅](http://www.freebuf.com/articles/others-articles/36927.html)——翻译：赵阳


### CTF练习平台
[i春秋 竞赛训练](https://www.ichunqiu.com/racing)
[ctf题目](http://captf.com/)
[XCTF_OJ练习平台](http://oj.xctf.org.cn)
[实验吧](http://www.shiyanbar.com/ctf/)
[网络信息安全攻防学习平台](http://hackinglab.cn/)
[白帽学院ctf挑战赛](http://www.baimaoxueyuan.com/ctf)
[合天ctf](http://www.hetianlab.com/CTFrace.html)


### 学习平台
[乌云](http://wooyun.org/)
[i春秋](https://www.ichunqiu.com/)
[w3school](http://www.w3school.com.cn/)
[廖雪峰](http://www.liaoxuefeng.com/)

### 学习论坛
[吾爱破解](http://www.52pojie.cn/)
[飘云阁](http://www.chinapyg.com/)
[看雪论坛](http://bbs.pediy.com/)

### 附：

[CTF预告](https://ctftime.org/event/list/upcoming)——CTFtime

#### 国际知名CTF赛事:
- **DEFCON CTF**：CTF赛事中的“世界杯”
- UCSB iCTF：来自UCSB的面向世界高校的CTF
- Plaid CTF：包揽多项赛事冠军的CMU的PPP团队举办的在线解题赛
- Boston Key Party：近年来崛起的在线解题赛
- Codegate CTF：韩国首尔“大奖赛”，冠军奖金3000万韩元
- Secuinside CTF：韩国首尔“大奖赛”，冠军奖金3000万韩元
- XXC3 CTF：欧洲历史最悠久CCC黑客大会举办的CTF
- SIGINT CTF：德国CCCAC协会另一场解题模式竞赛
- Hack.lu CTF：卢森堡黑客会议同期举办的CTF
- EBCTF：荷兰老牌强队Eindbazen组织的在线解题赛
- Ghost in the Shellcode：由Marauders和Men in Black Hats共同组织的在线解题赛
- RwthCTF：由德国0ldEur0pe组织的在线攻防赛
- RuCTF：由俄罗斯Hackerdom组织，解题模式资格赛面向全球参赛，解题攻防混合模式的决赛
- RuCTFe：由俄罗斯Hackerdom组织面向全球参赛队伍的在线攻防赛
- PHD CTF：俄罗斯Positive Hacking Day会议同期举办的CT

#### 国内知名CTF赛事:
- **XCTF**全国联赛：
中国网络空间安全协会竞评演练工作组主办、南京赛宁承办、KEEN TEAM协办的全国性网络安全赛事平台
2014-2015赛季五站选拔赛分别由清华、上交、浙大、杭电和成信技术团队组织
（包括杭电HCTF、成信SCTF、清华BCTF、上交0CTF和浙大ACTF）
XCTF联赛总决赛由蓝莲花战队组织，XCTF联赛提供100万元奖励池，
是国内最权威、最高技术水平与最大影响力的网络安全CTF赛事平台。
- **AliCTF**：由阿里巴巴公司组织，面向在校学生的CTF竞赛，冠军奖金10万元加BlackHat全程费用。
- XDCTF：由西安电子科技大学信息安全协会组织的CTF竞赛，其特点是偏向于渗透实战经验。
- HCTF：由杭州电子科技大学信息安全协会承办组织的CTF。
杭州电子科技大学信息安全协会由杭州电子科技大学通信工程学院组织建立，协会已有七年历史，
曾经出征DEFCON,BCTF等大型比赛并取得优异成绩，同时协会还有大量有影响力的软件作品。
协会内部成员由热爱黑客技术和计算机技术的一些在校大学生组成，
有多个研究方向，主要有渗透，逆向，内核，web等多个研究方向。
至今已经成功举办6次CTF比赛。
- ISCC：由北理工组织的传统网络安全竞赛，最近两年逐渐转向CTF赛制。


## 参考来源：（如有侵权联系删除）
  1. [CTF 各类资源大整合](http://blog.csdn.net/the__blue__sky/article/details/51763080)——不改长流
  2. [CTF——百度百科](http://baike.baidu.com/item/ctf/9548546)
  3. [CTF的过去、现在与未来](https://www.ichunqiu.com/course/1055)——诸葛建伟

  最后更新时间：2017年5月27日 23:51:29