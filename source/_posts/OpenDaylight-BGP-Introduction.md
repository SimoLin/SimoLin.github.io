---
title: OpenDaylight BGP 简介(OpenDaylight BGP Introduction)
date: 2018-02-17 20:47:14
tags:
- OpenDaylight
categories:
- OpenDaylight
---

偶然学习了一点点

- ODL(OpenDaylight)
- BGP(Border Gateway Protocol,边界网关协议)

学得皮毛，总结一下也算一个小收获
如果能对其他人有所帮助，那就更好了


【本文仅为学习目的，如有侵权，请联系我删除】
<!-- more -->

# 前言

随着互联网的普及和用户数量的剧增，原本的网络不堪重负。
移动终端发展势如破竹，智能手机不断更新换代，手机软件层出不穷，用户随时随地上网，
导致流量需求与日俱增，负荷过度的网络无法满足用户需求。

原有的网络体系庞大，架构臃肿，不够灵活，不能适应不断涌现出的新业务需求，服务质量得不到保证。
网络体系复杂，网络操作需要与其他IT操作的集成与协作，导致网络部署困难。
网络更新麻烦，动手操作过多，网络管理员分身乏术。
改良已经无法解决现有的网络问题，网络改革势在必行，于是SDN应运而生。

SDN是美国斯坦福大学clean slate研究组提出的一种新型网络架构。
传统网络采用分布式策略工作，由设备制定转发策略，而SDN架构中设备不运行任何协议，
转发表由控制器下发给设备，实现数据平台与控制平台的分离。
SDN的核心思想就是控制与转发分离，将软件应用到网络控制中，并起到主导作用，而不是由固定模式的协议控制网络。
SDN的目的是提高网络的可控性与可编程性，可以根据用户需求灵活地提供不同质量等级的服务。

ODL是由Linux基金会推出的一个开源项目，集聚了行业中领先的供应商和Linux基金会的一些成员。
其目的在于通过开源的方式创建共同的供应商支持框架，不依赖于某一个供应商，
竭力创造一个供应商中立的开放环境，每个人都可以贡献自己的力量，从而不断推动SDN的部署和创新。
打造一个共同开放的SDN平台，在这个平台上进行SDN普及与创新，供开发者来利用、贡献和构建商业产品及技术。
ODL的终极目标是建立一套标准化软件，帮助用户以此为基础开发出具有附加值的应用程序。

# OpenDaylight（ODL）

OpenDaylight，当今最大的开源SDN控制器项目，正推动SDN思想的实现，加速网络领域的创新。

## ODL控制器

ODL拥有一套模块化、可插拔灵活地控制平台作为核心，
这个控制平台基于Java开发，理论上可以运行在任何支持Java的平台上，
从Helium版本开始其官方文档推荐的最佳运行环境是最新的Linux（Ubuntu 12.04+）及JVM1.7+。

ODL控制器采用OSGi框架，OSGi框架是面向Java的动态模型系统，它实现了一个优雅、完整和动态的组件模型，
应用程序（Bundle）无需重新引导可以被远程安装、启动、升级和卸载，
通过OSGi捆绑可以灵活地加载代码与功能，实现功能隔离，解决了功能模块可扩展问题，
同时方便功能模块的加载与协同工作。自Helium版本开始使用Karaf架构，作为轻量级的OSGi架构，
相较于早前版本的OSGi提升了交互体验和效率，当然其特性远不仅仅于此。

ODL控制平台引入了SAL（服务抽象层 ），SAL北向连接功能模块，以插件的形式为之提供底层设备服务，
南向连接多种协议，屏蔽不同协议的差异性，为上层功能模块提供一致性服务，使得上层模块与下层模块之间的调用相互隔离。
SAL可自动适配底层不同设备，使开发者专注于业务应用的开发。

此外，ODL从Helium开始也逐渐完成了从AD-SAL（Application Driven Service Abstraction Layer）向MD-SAL（Model Driven Service Abstraction Layer）的演进工作，
早前的AD-SAL，ODL控制平台采用了Infinispan技术，Inﬁnispan是一个高扩展性、高可靠性、键值存储的分布式数据网格平台，
选用Infinispan来实现数据的存储、查找及监听，用开源网格平台实现controller的集群。
MD-SAL架构中采用Akka实现分布式messageing。

## ODL控制器架构

如图所示，ODL控制器主要包括开放的北向API，控制器平面，以及南向接口和协议插件。
北向API有OSGI和REST两类，同一地址空间应用使用OSGI类，而不同地址空间的应用则使用REST类。
OSGI是有状态的连接，有注册机制，而rest是无状态链接。
上层应用程序利用这些北向API获得网络智能信息、运行算法进行分析并且设计部署新的网络策略。

{% asset_img 1.png %}

控制器平台包括一系列功能模块，可动态组合提供不同服务。
其中主要包括拓扑管理、转发管理、主机监测、交换机管理等模块。
服务抽象层SAL是控制器模块化的核心，自动适配底层不同的设备，使开发者专注于业务应用的开发。
SAL北向连接功能模块，以插件的形式为之提供底层设备服务。
南向连接多种协议插件，屏蔽不同协议的差异性，为北向功能模块提供一致性服务，SAL起到中间调度作用。

南向接口支持多种不同协议，如openflow1.0、openflow1.3、BEG-LS等。底层支持混合模式交换机和经典openflow交换机。

## ODL控制器设计原则

ODL在设计的时候遵循了六个基本的架构原则
（以下来自OpenDaylight官方文档）：
1. 运行时模块化和扩展化（Runtime Modularity and Extensibility）：
支持在控制器运行时进行服务的安装、删除和更新。
2. 多协议的南向支持（Multiprotocol Southbound）：
南向支持多种协议。
3. 服务抽象层（Service Abstraction Layer）：
南向多种协议对上提供统一的北向服务接口。
Hydrogen中全线采用AD-SAL，Helium版本AD-SAL和MD-SAL共存，Lithium和Beryllium中已基本使用MD-SAL架构。
4. 开放的可扩展北向API（Open Extensible Northbound API）：
提供可扩展的应用API，通过REST或者函数调用方式。两者提供的功能要一致。
5. 支持多租户、切片（Support for Multitenancy/Slicing）：
允许网络在逻辑上（或物理上）划分成不同的切片或租户。
控制器的部分功能和模块可以管理指定切片。
控制器根据所管理的分片来呈现不同的控制观测面。
6. 一致性聚合（Consistent Clustering）：
提供细粒度复制的聚合和确保网络一致性的横向扩展（scale-out）。

## ODL及BGP安装

- 首先下载安装包
下载地址为(推荐从官网下载):
https://www.opendaylight.org/technical-community/getting-started-for-developers/downloads-and-documentation
- 本文使用的ODL版本为Carbon SR2 October 16, 2017
- 文件名为distribution-karaf-0.6.2-Carbon.zip

### Windows系统下安装ODL

直接解压得到压缩文件里的内容如下

{% asset_img 2.png %}

运行\bin目录下karaf.bat启动，等待进入控制台，

{% asset_img 3.png %}

看到如下界面表示成功进入ODL控制台

{% asset_img 4.png %}

### Linux系统下安装ODL

此处略去安装Linux系统过程
本文使用的Linux系统版本为：Ubuntu x64 16.10(虚拟机)

{% asset_img 5.png %}

同样解压下载到的distribution-karaf-0.6.2-Carbon.zip

{% asset_img 6.png %}

运行\bin目录下karaf启动，等待进入控制台

{% asset_img 7.png %}

看到如下界面表示成功进入ODL控制台

{% asset_img 8.png %}

### 安装BGP

在ODL安装完毕之后，可以开始安装BGP
在ODL控制台输入命令feature:install odl-bgpcep-bgp 回车执行命令
成功执行并安装完毕，无回显

{% asset_img 9.png %}

如不确定是否安装完成，可以执行命令feature:list -i 查询已安装的feature

{% asset_img 10.png %}

# BGP (Border Gateway Protocol)

前文中我们已经安装了odl-bgpcep-bgp。
此命令将安装BGP（边界网关协议）建立连接所需的一切，
并将数据存储在RIB（路由信息库）中，并在网络拓扑总览中显示数据。

BGP全称是Border Gateway Protocol, 对应中文是边界网关协议。
BGP是互联网上一个核心的去中心化自治路由协议。
它的地位是核心的（目前是最重要的，互联网上唯一使用的路由协议），它的目的是去中心化，以达到各个网络自治。

## BGP架构(Architecture)

每个特性（feature）都代表了BGPCEP代码库中的一个模块。
下图则说明了这些功能是如何相关的。

{% asset_img 11.png %}

## BGP概念(concepts)及数据结构

此模块包含RFC 4271，RFC 4760，RFC 4456，RFC 1997和RFC 4360中描述的基本BGP概念。所有的概念都在一个yang模型中描述：bgp-types.yang。
如下图所示（图为yang模型部分内容）
全文可查阅：
https://git.opendaylight.org/gerrit/gitweb?p=bgpcep.git;a=blob;f=bgp/concepts/src/main/yang/bgp-types.yang;hb=refs/heads/stable/boron

{% asset_img 12.png %}

除此之外，只有一个NextHopUtil类，它包含序列化和解析NextHop的方法。

对于该yang模型和类，在ODL安装目录下可以找到对应的jar文件，
即system\org\opendaylight\bgpcep\concepts\0.7.2-Carbon\concepts-0.7.2-Carbon.jar文件

{% asset_img 13.png %}

## BGP协议分析

BGP是应用层协议，其传输层使用TCP，默认端口号是179。
因为是应用层协议，可以认为它的连接是可靠的，并且不用考虑底层的工作，例如fragment，确认，重传等等。
BGP是唯一使用TCP作为传输层的路由协议，其他的路由协议可能都还到不了传输层。

TCP连接的窗口是65K字节，也就是说TCP连接允许在没有确认包的情况下，连续发送65K的数据。
而其他的路由协议，例如EIGRP和OSPF的窗口只有一个数据包，
也就是说前一个数据包收到确认包之后，才会发送下一个数据包。
当网络规模巨大时，需要传输的数据也相应变大，这样效率是非常低的。
这也是它们不适合大规模网络的原因。

而正是由于TCP可以可靠的传输大量数据，且互联网的路由信息是巨大的，
TCP被选为BGP的传输层协议，并且BGP适合大规模网络环境。

### 消息头格式（Message Header Format）

和大部分协议一样，BGP的数据由header和data组成。
Header有19个字节，所有的BGP数据的Header格式是一样的。

{% asset_img 14.png %}

Marker有16个字节长，存储着同步信息和加密信息。
Length有2个字节，包含header在内的长度。
Type有1个字节，表示当前BGP数据的类型，具体有4类：
1. Open（code 1）：TCP连接建立之后，BGP发送的第一个包。收到Open之后，BGP peer会发送一个Keepalive消息以确认Open。其他所有的消息都只会在Open消息确认之后，并且BGP连接已经建立之后发送。
2. Update（code 2）：BGP连接后的首次Update会交换整个BGP route table，之后的Update只会发送变化了的路由信息。所以说BGP是动态的传输路由消息的变化。
3. Notification（code 3）：出错时发送的消息，这个消息一旦发送，BGP连接将会中断。
4. Keepalive（code 4）：没有data，只有header。用来保持BGP连接，通常是1/3的BGP session hold time。默认是60秒，如果hold time是0，不会发送Keepalive。

### OPEN消息格式（OPEN Message Format）

TCP连接建立后，建立连接的双方发送的第一条消息都是OPEN消息。
如果OPEN消息是可接受的，将发送确认OPEN消息的KEEPALIVE消息。

除了固定大小的BGP头之外，OPEN消息还包含以下字段：

{% asset_img 15.png %}

- Version：BGP的版本号。对于BGPv4来说，其值为4。
- My Autonomous System：本地AS编号。通过比较两端的AS编号可以确定是EBGP连接还是IBGP连接。
- Hold Time：在建立对等体关系时两端要协商Hold time，并保持一致。如果两端所配置的Hold time时间不同，则BGP会选择较小的值作为协商的结果。如果在这个时间内未收到对端发来的Keepalive消息，则认为BGP连接中断。如果保持时间为0，则标识不发送Keepalive报文。
- BGP Identifier：BGP路由器的Router ID，以IP地址的形式表示，用来识别BGP路由器。
- Opt Parm Len（Optional Parameters Length）：可选参数的长度。如果为0则没有可选参数。
- Optional Parameters：是一个可选参数用于BGP验证或多协议扩展（Multiprotocol Extensions）等功能。每一个参数为一个<参数类型，参数长度，参数值>（Parameter Type-Parameter Length-Parameter Value）三元组。

{% asset_img 16.png %}

- Parameter Type 为一个字节长度，表示参数的类型。
- Parameter Length 为一个字节长度，表示参数内容（Parameter Value）的长度。
- Parameter Value 不定长字节，根据参数不同内容不同。

### UPDATE消息格式（UPDATE Message Format）

UPDATE消息用于在BGP对等体之间传送路由信息。包含以下字段：

{% asset_img 17.png %}

- Withdrawn Routes Length ：（2字节无符号整数） 不可达路由长度，表示Withdrawn Routes字段的数据长度。如果Withdrawn Routes Length字段数值为0，则表示Withdrawn Routes字段没有任何数据，在UPDATE消息中不会被显示。
- Withdrawn Routes ：（变长） 撤销路由。该字段包括一系列的IP地址前缀信息，以下图的格式来表示，比如<19,198.18.160.0>表示一个198.18.160.0 255.255.224.0的网络。

{% asset_img 18.png %}

- Path Attribute Length ：（2字节无符号整数） 路由属性长度，表示Path Attribute字段的数据长度。如果Path Attribute Length数值为0，则表示Path Attribute字段没有任何数据，在UPDATE消息中不会被显示。

{% asset_img 19.png %}

- Network Layer Reachability Information ：（变长） 网络可达信息。包括一系列的IP地址前缀。格式与撤消路由字段一样。

### KEEPALIVE消息格式（KEEPALIVE Message Format）

- KEEPALIVE报文的组成只包括一个BGP数据报头。
- 缺省情况下，发送KEEPALIVE的时间间隔为 60 秒，Hold Time是180秒。每次从邻居处接收到KEEPALIVE报文将重置Hold Time定时器，如果Hold Time定时器超时，就认为对等体Down掉。

### NOTIFICATION消息格式（NOTIFICATION Message Format）

- Errorcode：错误码。1字节长的字段。每个不同的错误都使用唯一的代码表示，而每一个错误码都可以拥有一个或多个错误子码，但如果某些错误码并不存在错误子码的话，则该错误子码字段以全0表示。
- Errsubcode：错误子码。

## BGP工作原理

下图为BGP内部处理流程的简要流程图

{% asset_img 20.png %}

BGP是一种路径矢量协议（Path vector protocol）的实现。
因此，它的工作原理也是基于路径矢量。

首先说明一下，下面说的BGP route指的是BGP自己维护的路由信息，
区分于设备的主路由表，也就是我们平常看见的那个路由表。
BGP route是BGP协议传输的数据，并存储在BGP router的数据库中。
并非所有的BGP route都会写到主路由表。
每条BGP route都包含了目的网络，下一跳和完整的路径信息。
路径信息是由AS号组成，当BGP router收到了一条路由信息，如果里面的路径包含了自己的AS号，
那它就能判定这是一条自己曾经发出的路由信息，收到的这条路由信息会被丢弃。

这里把每个BGP服务的实体叫做BGP router，而与BGP router连接的对端叫BGP peer。
每个BGP router在收到了peer传来的路由信息，会存储在自己的数据库，
前面说过，路由信息包含很多其他的信息，BGP router会根据自己本地的policy结合路由信息中的内容判断，
如果路由信息符合本地policy，BGP router会修改自己的主路由表。本地的policy可以有很多，
举个例子，如果BGP router收到两条路由信息，目的网络一样，但是路径不一样，
一个是AS1->AS3->AS5，另一个是AS1->AS2，如果没有其他的特殊policy，BGP router会选用AS1->AS2这条路由信息。
policy还有很多其他的，可以实现复杂的控制。

除了修改主路由表，BGP router还会修改这条路由信息，将自己的AS号加在BGP数据中，
将下一跳改为自己，并且将自己加在路径信息里。
在这之后，这条消息会继续向别的BGP peer发送。
而其他的BGP peer就知道了，可以通过指定下一跳到当前BGP router，来达到目的网络地址。

## 总结

经过对BGP数据结构、消息格式、协议、工作原理的分析，
BGP更像是一个可达协议，可达信息传来传去，本地根据收到的信息判断决策，
对信息进行处理，再应用到路由表，最终发送给其他BGP结点。

BGP协议在整个过程中都扮演着十分重要的角色，
贯穿着整个接收、决策、处理、发送的过程，起到了至关重要的作用。

# 参考资料

非常感谢以下列出的与未列出的各篇文章与资料，本文内容为学习了以下资料的产物
再次表示忠心的感谢

本文中也引用了以下资料中的部分图片、文字等
【本文仅为学习目的，如有侵权，请联系我删除】

1. 官网：http://www.opendaylight.org
2. 代码库：http://git.opendaylght.org
3. wiki:http://wiki.opendaylight.org
4. jira:http://jira.opendaylight.org
5. FAQ:http://faq.opendaylight.org
6. [installing_opendaylight](http://docs.opendaylight.org/en/stable-carbon/getting-started-guide/installing_opendaylight.html)
7. [BGP Developer Guide](http://docs.opendaylight.org/en/stable-carbon/developer-guide/bgp-developer-guide.html)
8. [bgp-user-guide](http://docs.opendaylight.org/en/stable-carbon/user-guide/bgp-user-guide.html)
9. [bgp-monitoring-protocol-user-guide](http://docs.opendaylight.org/en/stable-carbon/user-guide/bgp-monitoring-protocol-user-guide.html)
10. [Using ODL BGP and advertising routes ODL REST API and XML files](https://www.serro.com/opendaylight-using-odl-bgp-advertising-routes-odl-rest-api-xml-files/)
11. http://www.sdnlab.com
12. [人人都想了解的BGP，路由策略这样处理——肖宏辉](https://www.sdnlab.com/20315.html)
13. [BGP漫谈——肖宏辉](https://www.sdnlab.com/20293.html)
14. [OpenDaylight你不可不知的十大问题——OpenDaylight是什么？](https://www.sdnlab.com/16612.html)

最后更新时间：2018年2月22日 13:30:55
