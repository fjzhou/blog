---
layout: post
title: 输入网址后发生了什么
category: Note
date: 2017/03/18 11:00
---

> 一个经典的面试题：你输入网址后到页面显示出来，中间发生了什么？

<!--more-->

## 物理

先扯个淡。输入网址的时候当然首先动作的是键盘。如果是外接USB键盘，则是键盘读取按键信号，然后传递给操作系统。如果是触摸屏，则有操作系统负责识别触摸区域，得到按键信息。

## 操作系统

用户端常见的操作系统包括：微软的Windows、苹果的macOS、开源的Linux、Google的Android、苹果的iOS等。

操作系统负责管理所有硬件，提供程序执行环境，为上层程序提供基本的输入输出等功能。除了这些基本功能外（一般叫做操作系统内核），系统还会提供一些基础设施。比如网络Socket API，图形图像功能等。这些功能都是浏览器所需要的。

浏览器作为一个程序，从操作系统处得到用户输入的网址和输入完成的命令。后续的所有功能都需要调用操作系统对应的API来实现。

## 浏览器

常见的浏览器软件包括：

* Google的Chrome。Webkit内核（新版转向Blink）和V8 JS引擎
* 微软的IE。Trident内核，IE 11之后不再更新。
* 微软的Edge。EdgeHTML内核，Chakra JS引擎。
* Mozilla的Firefox。Gecko内核和SpiderMonkey JS引擎。
* 苹果的Safari。Webkit内核，Nitro JS引擎。
* Opera。原Presto，新版也转向Blink和V8。

浏览器接收到网址后，开始以下的流程。

## DNS

首先浏览器需要联系网站服务器。网址，即URL，被解析为若干部分。例如对于`www.baidu.com`，实际上它的完整形式是：`http://www.baidu.com:80/`。其中包括了以下部分：

* 协议：http
* 域名：www.baidu.com。其中`baidu.com`是百度的域名，www是这个域名下的二级域名。
* 端口：80
* 路径：/

其中协议部分默认为http，端口对于http来说默认是80，对于https来说是443。接下来如果域名部分不是IP的话，需要DNS协议得到服务器的IP地址。

DNS本身是一个基于UDP协议的网络协议。专门用于服务器和IP地址的查询。DNS服务由DNS服务器提供，默认端口是UDP的53。一个计算机的DNS服务器配置在操作系统中，是上网配置的基本元素之一（另外几个包括IP地址，子网掩码，默认网关）。

浏览器把域名发送给系统默认DNS服务器。如果该服务器本地有缓存，且缓存未过期，则直接返回结果。否则向上一级DNS服务器查询，直到DNS根服务器。DNS协议最终会返回A记录(IPv4)或者AAAA记录(IPv6)或者Alias(别名)等。如果DNS失败，浏览器会提示域名找不到或者DNS错误。

到此为止，浏览器知道了网址的对应服务器IP地址和端口，然后就通过TCP协议发起网络请求。但是发起的是什么请求取决于网址的协议是HTTP还是HTTPS。

## HTTP

HTTP是基于文本的协议，客户端和服务器一问一答，有固定格式。包括HTTP Header和Boby。头部使用换行符进行分隔字段，一行一个字段。字段中使用冒号`:`分隔Key和Value。例如：

```
GET / HTTP/1.1
Host: www.baidu.com
Connection: keep-alive
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_3) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.14 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
DNT: 1
Accept-Encoding: gzip, deflate, sdch, br
Accept-Language: zh-CN,zh;q=0.8,en;q=0.6,zh-TW;q=0.4
Cookie: BAIDUID=F27F5856EB13162499CC2DB7DC4FD050:FG=1;
```

最重要是开始的几行:

* GET / HTTP/1.1：使用GET动作（另外还有POST，PUT等），获得`/`路径的内容（还记得网址里最后的`/`么），采用`HTTP/1.1`协议
* Host: www.baidu.com：主机头为www.baidu.com。
* Connection: keep-alive：结束后保持网络连接，以节省TCP建立的开销。
* User-Agent：报告浏览器参数。
* Accept：浏览器能接受的应答格式。

这是百度服务器返回的Response Header。

```
HTTP/1.1 200 OK
Server: bfe/1.0.8.18
Date: Mon, 13 Mar 2017 12:23:41 GMT
Content-Type: text/html;charset=utf-8
Transfer-Encoding: chunked
Connection: keep-alive
Cache-Control: private
Expires: Mon, 13 Mar 2017 12:23:41 GMT
Content-Encoding: gzip
```

应答的主要字段：

* HTTP/1.1 200 OK：`HTTP/1.1`协议，返回代码200，含义为请求成功完成
* Server：百度服务器信息
* Date：时间
* Content-Type：应答的格式和编码。这是和浏览器请求对应的。
* Expires: 过期时间，在这个时间之前的话应答不会过期，允许浏览器进行本地缓存。

## HTTPS

HTTPS协议是在HTTP协议之下的加密层。客户端和服务器建立起一个加密通道后进行数据传输。传输的数据仍然是HTTP文本。一旦部署了HTTPS，那么数据就不再是明码，可以防止数据在传输过程中的泄露。另外，SSL证书颁发机构在颁发EV证书的时候，会严格验证机构身份，可以在一定程度上避免钓鱼网站。

> SSL证书分为DV，OV和EV三种。DV仅验证域名所有者，EV会验证机构身份。因此数据安全要求较高的网站，例如金融机构，一般都申请EV证书。访问的时候浏览器地址栏会出现绿色机构名称。
> ![工行网银](http://upload-images.jianshu.io/upload_images/1456355-d8a296d4559f739d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

部署正确的HTTPS Web服务器中有一个经过权威认证的数字证书。数字证书包含有服务器公钥和签名算法。浏览器和服务器通过非对称加密算法公私钥机制进行通信，协商出一个双方都支持的对称加密算法和加密的秘钥。

数字证书由一个叫做CA（Certification Authority）的机构颁发，包含由服务器公钥，订阅人相关信息(名称，域名等)，最后由颁发者使用自己的证书进行签名。CA的机构证书由另一个CA签名，直至最后一种特殊的证书为止：自己给自己签名。这种证书叫做根证书，预先内置在操作系统或者浏览器中。由此数字证书形成一个证书链。

![百度数字证书](http://upload-images.jianshu.io/upload_images/1456355-195c27beb0790e04.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

其中`baidu.com`证书负责百度域名下的HTTPS运作，此证书由一张`GlobalSign Organization Validation CA`的中间证书签名，而这张中间证书由`GlobalSign Root CA`即根证书颁发。

不由根证书直接签发是因为安全原因。因为根证书一般都内置于系统，更新这个列表是很慢的。而系统只验证CA是否可信任，只要受信任，它可以签发任何域名的证书。因此根证书私钥一旦泄露或发生其他问题造成证书不安全，后果就很严重。结果是根证书私钥一般存放在和银行金库一样安全的地方，不可能经常拿出来为客户签名。一般由根证书签发一些中间证书，平时由中间证书来签发终端证书，一旦出问题，可以直接吊销这张中间证书。

SSL证书的作用是构建一个可信任的加密数据通道，在网络中不止可在HTTP中使用。比如和FTP搭配使用就是FTPS。

## 网络接入

其实DNS也是需要通过网络的。但是网络放到现在来说吧。

接下来，数据需要通过网络发送出去。网络分为电信、移动、联通宽带和手机网络。以下是网络层的模型:

![网络模型](http://upload-images.jianshu.io/upload_images/1456355-2ac5b9c6103b9e56.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

不同的接入方式在物理层和网络链路层是不同的，但是到了IP层就都统一了。

* 有线宽带

电脑通过水晶头（RJ45）连接网线（双绞线），后连接到网络设备上（HUB、路由器、交换机等）。这是以太网的接入方式。

* 无线宽带

设备通过天线与接入点（Access Point，AP）通信，然后AP接入网络。AP后端一般通过以太网有线方式接入网络。即无线仅作为设备和网络末端的链接方式。

通信协议的事实标准是802.11家族，例如802.11g，802.11n，802.11ac等。

* 手机

设备通过天线与基站通信，然后基站接入网络。这是最复杂的一种方式，电信运营商的天线、基站、协议等的标准浩如烟海。目前还在运营的接入标准包括：GSM，CDMA，WCDMA，TDSCDMA，CDMA2000，TD-LTE，FDD-LTE等。

## 运营商

网络通过末端进入运营商网络之后，需要通过一整套设备和规则进行数据传递。主要网络设备就是路由器和交换机。

多台设备通过交换机组成一个子网。子网之间通过路由器连接。路由器在内部维护了一张路由表，当有数据包到达时，查询路由表，决定数据包通过哪一个网卡，传输到下一个子网去。无数个路由器相互连接，把数据包传输到最终节点。这部分内容资料最多是Cisco认证相关教材和产品说明。

* 海底光缆

这是属于物理层的内容，属于运营商重要的组成部分。网络信号最终需要线缆通信，目前主流的大带宽通信手段就是光纤和光缆。而跨洲的通信就是通过海底光缆。若干年前，台湾地震，海底光缆故障，中断了大量的海外网络访问。图为中国海底光缆分布：

![中国海底光缆](http://upload-images.jianshu.io/upload_images/1456355-c3a30a3c8c07387a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 机房

最终数据到达网站所在机房。

网站在机房一般会有多台服务器，通过交换机互相连接后通过路由器作为网关接入运营商。数据到达网关后会被进行过滤和处理，通过防火墙、入侵检测等网络安全方面的设备，然后通过负载均衡等设备，最终达到目标服务器。


![凌乱的机房](http://upload-images.jianshu.io/upload_images/1456355-c17ecf855df08efc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 服务器操作系统

与用户端一样，服务器也需要通过操作系统运行网站相关软件。不同的是服务器的操作系统是为长时间稳定高效运行特别优化过的。常见的服务器操作系统包括：Linux，Unix家族（FreeBSD，Solaris等），Windows Server（2003,2008,2012,2016）。

## Web服务器软件

终于到达处理HTTP协议的软件了。这类软件叫做Web Server。主流的包括Windows系统上的IIS，开源的Apache，Nginx等。

* HTTP协议处理

Web Server最重要的角色就是处理HTTP协议，给浏览器规范的应答。处理网络请求和相关连接。因此吞吐量和性能是一个重要的指标。开源软件中Nginx是高性能Web服务器的主流选择，IIS是Windows上的标准软件。

* HTTPS协议处理

HTTPS协议相关的证书、加解密、签名等也需要Web服务器处理。但是开源软件中，一般选择调用`OpenSSL`处理，而IIS是微软自行实现的加密库。

* 静态文件

Web Server能够直接处理的只有静态文件。比如请求`http://upload.jianshu.io/admin_banners/web_images/2870/48782c3773b1132ee6c594e56211f64499669099.jpg`，那么Web服务器根据网址在服务器磁盘上找到这个路径的文件，返回文件内容。

* 动态内容接口

但是现在的网站除了css、js和图片等资源外，大量的内容都是动态内容。Web服务器需要一个标准接口与不同语言的代码配合，来动态生成网页内容。这个接口包括CGI、FastCGI、ISAPI等。其中ISAPI属于IIS，开源服务器一般支持CGI和FastCGI等。

## CDN和代理

在进入动态内容之前，插入一段静态资源处理的现状。

一般来说，静态资源很少变化，而请求量很大。而通过前文可以知道用户和服务器之间有众多的网络设备和线路需要通过。为了加速静态资源（尤其是图片）的访问，服务器离用户越近越好。因此产生了CDN这个需求。

CDN即内容分发网络，通过在各地部署服务器，在服务器之间同步静态资源，然后就近提供给用户。从域名上就能看出简书用了CDN。比如Logo的网址：`http://cdn-qn0.jianshu.io/assets/web/logo-58fd04f6f0de908401aa561cda6a0688.png`

## 应用服务器

请求到达应用服务器之后，就开始网站自己的活了。应用服务器通过CGI、FastCGI、ISAPI等接口与Web服务器通信，获得HTTP请求，返回HTTP应答。主流应用服务器根据语言和平台分：

* Windows和.NET：IIS。IIS放在这里是因为ASP.NET深度集成，无法分出。
* Java：Jetty，JBoss，Tomcat，WebLogic等
* PHP：php-cgi

应用服务器可以作为dll运行在Web服务器中（比如IIS，但是IIS也支持FastCGI），或者作为独立进程，通过IPC与Web服务器通信。 

接下来就是程序内部的事情了。省略。

## 数据库服务器

应用服务器在生成网站内容的过程中一般需要连接数据库存取数据。目前主流数据库包括：
* MySQL：开源软件，SUN公司被收购后由Oracle负责
* MariaDB：SUN被收购后，因为社区与Oracle的纠纷，从MySQL分支出来的版本。
* SQL Server：微软开发的商用数据库。其中Express  Edtion免费。
* Oracle：Oracle公司开发的商用数据库。
* PostgreSQL：开源软件，号称开源中功能最强。
* DB2：IBM公司开发的商用数据库。
* Redis: NoSQL，支持多种数据结构
* MongoDB: NoSQL

## HTML和CSS解析

最终应用服务器生成了HTML，然后给Web Server。Web Server生成HTTP应答，然后通过复杂的网络线路回到用户电脑中。用户电脑的操作系统把应答给浏览器。浏览器开始渲染。

以下是浏览器渲染的主要过程：

![渲染](http://upload-images.jianshu.io/upload_images/1456355-8883dbd15f39cc18.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

开始从头到尾解析HTML，并生成DOM树。其中如果有外链资源，需要再次通过网络层以此下载。如果是`<script>`标签，则需要调用JS引擎执行脚本。如果是css，则开始解析CSS，得到一系列样式规则。DOM树生成之后，与样式规则拼接，得到渲染树。

渲染树经过排版引擎得到最终的渲染位置和样式，然后调用操作系统的绘图API绘制页面（部分内容会调用显卡进行硬件加速）。

## JavaScript引擎

现代浏览器中JS部分越来越重要，因为前端中JS的比例越来越大。其中Google Chrome内置的V8引擎已经超越浏览器，在`node.js`、桌面开发等其他领域也应用广泛。比如我现在写这篇文章用的`Visual Studio Code`就内置V8引擎。

JS引擎负责解释并执行JS语言（现代浏览器一般内置JIT编译器以加速执行），并通过浏览器或者其他环境提供API给JS使用。浏览器中最重要的API就是DOM（Document Object Model）和BOM（Browser Object Model）。JS正是通过这些接口来操作HTML，并在此基础上获得动态页面的效果。
