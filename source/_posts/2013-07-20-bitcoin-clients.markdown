---
layout: post
title: "比特币客户端与钱包"
date: 2013-07-20 17:00
comments: true
categories: 
---
### 简介

拥有和保存比特币，需要通过客户端，通常把该软件称为钱包。目前，整个比特币项目由`Bitcoin Foundation`来开发与维护，通常把他们称为官方团队。官方推出的客户端是`Bitcoin Qt`, 由C++编写核心功能，GUI界面由Python Qt完成。不含有GUI界面的被成为`bitcoind`，许多服务与核心功能均由其实现。运行bitcoind的通常称为节点(Bitcoin Node), 一个节点通常拥有完整的BlockChain数据，并实时与外界网络同步更新。

钱包中通常含有：

*  公钥、私钥、地址
*  与钱包中地址相关的交易信息
*  其他辅助数据

最核心的数据就是密钥，拥有密钥便拥有一切，相关信息均可由其而来。钱包并不一定需要包含完整的BlockChain数据，不包含BlockChain数据的钱包称为轻钱包(Light Weight Wallet)。对于大部分日常使用来讲，轻钱包便足够了。

### 分类

* 完全节点型(Full Node)：含有BlockChain所有完整数据
* 简易节点型(SPV Node)：Header-Only Clients，仅有Block头部信息，无需交易数据
* CS型(Server-Client)：服务端-客户端模式，大部分数据存储在服务端
* BS型：所有数据均通过浏览器在线使用

Bitcoin Qt，因为其是一款完整的钱包软件，需要下载大约超过`10GB`的BlockChain数据(24万个block)，对于大部分人来讲，是没有必要的。目前，官方主页上默认推荐的客户端已经不再是Bitcoin Qt, 而是[MultiBit](https://multibit.org)（支持Windows, MacOS和Linux的轻钱包）；移动端目前最好用的是[Bitcoin Wallet](https://play.google.com/store/apps/details?id=de.schildbach.wallet)(安卓平台)，iOS平台由于政策原因，一直未有出色的软件，Blockchain.Info为iOS提供了一个简单的钱包软件，[Blockchain for the iPhone](http://itunes.com/apps/blockchain)。还有就是在线钱包，如优秀的[BlockChain.Info](https://blockchain.info)，其安全性均超过自行保存管理，推荐使用之。

最近还有一种流行的钱包：脑钱包。因其安全性较低，并不推荐大家使用，仅临时性场合使用之。其原理是由一串密码短语，通过Hash运算，得到密钥，只要记住这串密语即可使用钱包。因为密码短语符合大家习惯和记忆特点，可以通过计算大量常见组合来破解。除了暴力破解的问题外，失忆是最大的风险，比如摔个跟头跌成脑震荡，或长期不用自然忘得一干二净。

### 选择&存储

*  日常使用的额度通常小于10个币，可以存放在电脑或手机App中。通常存放1个币以下是比较保险的，丢了不太心疼嘛

*  持有几十、几百个币的，可以选择BlockChain.Info，Inputs.io等在线钱包。其也可以当做日常钱包使用。

*  持有上千甚至数万的，应该分开存储，并隔离存放。使用离线电脑生产密钥，打印出来托管至银行等高安全场所存储，并销毁现有密钥。同时还需要多份隔离存储，甚至对密钥进行加密。

密钥即一切，如不慎弄丢钱包，便永远失去这笔比特币。所以钱包需要小心妥善保管，不在自己的PC或者手机App中存储大量比特币，丢失的风险太高，病毒木马、硬件损坏、手机丢失等均造成无法挽救的损失。俗话讲鸡蛋不要搁在一个篮子里，多种方式存储也是降低风险的有效方式。目前丢失的比特币或有数百万BTC之巨。

### 常见钱包

*  [Bitcoin-Qt](https://en.bitcoin.it/wiki/Bitcoin-Qt) - 官方客户端，基于C++/Qt，全平台，完全数据。
*  [MultiBit](https://en.bitcoin.it/wiki/MultiBit) - 全平台，轻钱包，官方推荐
*  [Electrum](https://en.bitcoin.it/wiki/Electrum) - 著名轻钱包
*  [Armory](https://en.bitcoin.it/wiki/Armory) - 基于Python，含有诸多特性的轻钱包
*  [BlockChain.info](https://blockchain.info) - 非常著名在线钱包

### 开发库

*  [bitcoind](https://en.bitcoin.it/wiki/Bitcoind) - 官方客户端，无GUI，开发者必备
*  [libcoin](https://github.com/libcoin/libcoin) - libcoin
*  [libbitcoin](https://github.com/spesmilo/libbitcoin) - asynchronous C++ library for Bitcoin
*  [cbitcoin](https://github.com/MatthewLM/cbitcoin) - A low-level bitcoin library written in standard C
*  [Bitcoinj](https://code.google.com/p/bitcoinj/) - a Java implementation of the Bitcoin protocol
*  [gocoin](https://github.com/piotrnar/gocoin) - Bitcoin client library for Go / golang
*  [pynode](https://github.com/jgarzik/pynode) - Bitcoin P2P router, in python
*  [bitcointools](https://github.com/gavinandresen/bitcointools) - Python-based tools for the Bitcoin cryptocurrency system，By Gavin Andresen
*  [bitcoin-abe](https://github.com/jtobey/bitcoin-abe) - Abe: block browser for Bitcoin and similar currencies

### 数据检索
*  [BlockChain.info](https://blockchain.info)
*  [Bitcoin Block Explorer](http://blockexplorer.com/)

* * *

#### 参考

1.  Bitcoin Foundation： [https://bitcoinfoundation.org/](https://bitcoinfoundation.org/)
1.  Why Apple Is Afraid Of Bitcoin: [http://www.forbes.com/sites/jonmatonis/2012/06/13/why-apple-is-afraid-of-bitcoin/](http://www.forbes.com/sites/jonmatonis/2012/06/13/why-apple-is-afraid-of-bitcoin/)
1.  List of Bitcoin-related software： [https://en.bitcoin.it/wiki/Software](https://en.bitcoin.it/wiki/Software)
1.  Bitcoin Clients: [https://en.bitcoin.it/wiki/Clients](https://en.bitcoin.it/wiki/Clients)
