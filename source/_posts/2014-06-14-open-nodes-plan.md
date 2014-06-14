title: '开放节点计划'
date: 2014-06-14 11:37:31
tags:
---

开放节点(Open Nodes)是一个社区项目，旨在搭建比特币枢纽节点。

为什么做这件事情？原因有：

* 比特币节点数量近一年急剧下降，从250K+减至7K
* P2P网络中的枢纽节点可以发挥很大作用
* 研究改进比特币P2P网络传播

计划做的事情有：

* 提供枢纽比特币节点，单机连接数高达2K~10K，优先部署国内节点
* 提供区块数据文件下载，加速新节点加入网络
* 提供部分RPC服务(基于Bitcoind)
* 提供一个WEB服务，供大家查阅节点数据、资料等

对比特币可能的益处有：

* 强大的节点覆盖率，对比特币网络数据一致性、实时性、安全性均有提升
* 商家、钱包、交易所、矿池等接入的枢纽节点，可以降低部分攻击，提升服务商的安全性

项目小组是完全开放的、非盈利的，任何人均可参与协作。项目自身完全依靠捐赠才能运作，网站、代码、资料、收支等均在Github公开。

项目由我（潘志彪）个人发起，计划步骤如下：

1. 提出项目，接收大家捐赠
2. 当筹集达到10个比特币后，正式启动。无法满足，则退还所有币
3. 公开透明的社区化运作

> 开放节点捐赠地址是： *[1CQUH3rYPYLdn8Sg5TRjzaCn4zrvjNE8RU](https://blockchain.info/address/1CQUH3rYPYLdn8Sg5TRjzaCn4zrvjNE8RU)*

### Update
* 2014-06-14 
  * 如需公开捐赠，建议使用[blockchain.info](https://blockchain.info)，发款时添加_Public Note_
  * 暂不支持LTC等其他币种

### Q&A

Q: 每年需要捐赠的规模？

_A: 项目一年支出大约5~25万，当前需12~60比特币。阿里云平台上一台[典型配置服务器](https://cloud.githubusercontent.com/assets/514951/3277543/f05a3c18-f37d-11e3-8c3b-e5b9d68e61e0.png "典型配置服务器")大约2.5万/年，项目所有成员都是免费工作、没有薪水，主要开支将会是购买服务器和相关服务。捐赠的币周期兑换为法币，已满足现金需要。_

Q: 计划搭建多少的节点？

_A: 枢纽节点追求单机性能而不是数量。全球节点目前[不足8000个](https://getaddr.bitnodes.io/nodes/1402721100/)，数个枢纽节点就可以达到非常高的全网覆盖率，所以，开放节点未来应该在20个以内，国内可能仅需要2~5个。（这只是大概，具体数量还需要依据实施的情况来评估，没数据没法可靠评估）。_

Q: 捐助过少、过多怎么处理？

_A: 捐赠过少，会缩减服务器数量，直至项目关闭；捐赠过多，会将多余的比特币捐出去，如：Wikipedia、Apache、OpenSSL等。项目启动之后，捐赠恕不退还。_

### 其他

* 已注册域名：*open-nodes.org*（不带'-'的已经被注册），尚未建立站点

* 为防止捐赠地址被恶意修改，特将比特币地址写入了域名的TXT记录，**donation.open-nodes.org**，_dig_记录如下：

````
$ dig -t txt @8.8.8.8 donation.open-nodes.org
;; QUESTION SECTION:
;donation.open-nodes.org.	IN	TXT
;; ANSWER SECTION:
donation.open-nodes.org. 564	IN	TXT	"1CQUH3rYPYLdn8Sg5TRjzaCn4zrvjNE8RU"

;; Query time: 368 msec
;; SERVER: 8.8.8.8#53(8.8.8.8)
;; WHEN: Sat Jun 14 13:57:34 2014
;; MSG SIZE  rcvd: 88
````
