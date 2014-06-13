---
layout: post
title: "比特币是什么?"
date: 2013-07-20 01:37
comments: true
categories: 
---
### 简介

比特币是一个自由软件项目，是一种基于密码学的电子货币，同时也是一种协议。整个系统采用去中心化思想构建，基于P2P模式运行。
其特点有：

*  去中心化发行与运作，无央行存在
*  货币不可伪造，无法多重支付，交易不可逆转
*  紧缩货币，总量固定，但可以无限分割
*  全球无障碍流通，快速支付且成本极低
*  账户匿名，且任何人均无法冻结，无法收税
*  天然审计


比特币由*Satoshi Nakamoto*创造（现已匿名消失），2008年11月1日，Satoshi在一个密码学的邮件列表中贴出了一篇论文："[Bitcoin P2P e-cash paper](http://www.mail-archive.com/cryptography@metzdowd.com/msg09959.html)"：

![qq20130720-1](https://f.cloud.github.com/assets/514951/827512/56e0e704-f09e-11e2-8957-573b9edbdafc.png)

### 创世纪块

2009年1月3日，第一个数据块(Genesis‎ Block)的生成宣告比特币系统诞生。Satoshi在Genesis Block数据区中写入如下信息：

{% codeblock %}
$ hexdump -n 255 -C blk00000.dat
00000000  f9 be b4 d9 1d 01 00 00  01 00 00 00 00 00 00 00  |................|
00000010  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
00000020  00 00 00 00 00 00 00 00  00 00 00 00 3b a3 ed fd  |............;...|
00000030  7a 7b 12 b2 7a c7 2c 3e  67 76 8f 61 7f c8 1b c3  |z{..z.,>gv.a....|
00000040  88 8a 51 32 3a 9f b8 aa  4b 1e 5e 4a 29 ab 5f 49  |..Q2:...K.^J)._I|
00000050  ff ff 00 1d 1d ac 2b 7c  01 01 00 00 00 01 00 00  |......+|........|
00000060  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
00000070  00 00 00 00 00 00 00 00  00 00 00 00 00 00 ff ff  |................|
00000080  ff ff 4d 04 ff ff 00 1d  01 04 45 54 68 65 20 54  |..M.......EThe T|
00000090  69 6d 65 73 20 30 33 2f  4a 61 6e 2f 32 30 30 39  |imes 03/Jan/2009|
000000a0  20 43 68 61 6e 63 65 6c  6c 6f 72 20 6f 6e 20 62  | Chancellor on b|
000000b0  72 69 6e 6b 20 6f 66 20  73 65 63 6f 6e 64 20 62  |rink of second b|
000000c0  61 69 6c 6f 75 74 20 66  6f 72 20 62 61 6e 6b 73  |ailout for banks|
000000d0  ff ff ff ff 01 00 f2 05  2a 01 00 00 00 43 41 04  |........*....CA.|
000000e0  67 8a fd b0 fe 55 48 27  19 67 f1 a6 71 30 b7 10  |g....UH'.g..q0..|
000000f0  5c d6 a8 28 e0 39 09 a6  79 62 e0 ea 1f 61 de     |\..(.9..yb...a.|
{% endcodeblock %}

还原出文本为：

    The Times 03/Jan/2009 Chancellor on brink of second bailout for banks

该段文字为英国《泰晤士报》2009年01月03日头版新闻标题：

![twcach2](https://f.cloud.github.com/assets/514951/828043/35469eca-f0aa-11e2-9726-f509586f4e7b.jpg)

这段文字暗示了比特币的诞生时间，并从中可以看出Satoshi对现有货币系统的强烈不满。

经过数年发展，已有大量计算机加入，形成了庞大的P2P网络，并构成巨大的算力屏障。除非小行星撞击地球，否则没有任何个人与组织能够关闭该系统。截止2013年7月19日，目前全网节点数约16万个：

![qq20130720-2](https://f.cloud.github.com/assets/514951/827955/f049f2e2-f0a7-11e2-8229-0a85976f08ef.png)

### 一切才刚刚开始

由于比特币的诸多颠覆特性，被人称为“史上最危险的自由软件项目”，现在经历数次大波折的比特币不仅没有衰落反而越来越繁荣，无数人正投身于这场伟大的社会实践中去，而比特币将开创出人类新的货币史。


* * *

#### 参考

1.  An open source P2P digital currency: [http://bitcoin.org/](http://bitcoin.org/)
1.  Bitcoin: A Peer-to-Peer Electronic Cash System: [http://bitcoin.org/bitcoin.pdf](http://bitcoin.org/bitcoin.pdf)
1.  Satoshi Nakamoto: [https://en.bitcoin.it/wiki/Satoshi_Nakamoto](https://en.bitcoin.it/wiki/Satoshi_Nakamoto)
1.  Bitcoin P2P Currency: The Most Dangerous Project We've Ever Seen：[http://launch3.squarespace.com/blog/l019-bitcoin-p2p-currency-the-most-dangerous-project-weve-ev.html](http://launch3.squarespace.com/blog/l019-bitcoin-p2p-currency-the-most-dangerous-project-weve-ev.html)
1. Genesis Blcok: [https://en.bitcoin.it/wiki/Genesis_block](https://en.bitcoin.it/wiki/Genesis_block)
1. Chancellor Alistair Darling on brink of second bailout for banks: [http://www.thetimes.co.uk/tto/business/industries/banking/article2160028.ece](http://www.thetimes.co.uk/tto/business/industries/banking/article2160028.ece)
1. Bitnods: [http://getaddr.bitnodes.io/](http://getaddr.bitnodes.io/)
