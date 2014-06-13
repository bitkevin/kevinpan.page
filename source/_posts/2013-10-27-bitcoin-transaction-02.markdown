---
layout: post
title: "比特币交易构成（二）"
date: 2013-10-27 19:54
comments: true
categories: 
---

## 交易的构造、签名与广播

上篇介绍了交易结构、签名等，为了更直观的认识比特币，借助**bitcoind**演示手动构造并广播交易的完整过程。

### 普通交易

#### 1. 找出未花费的币（unspent output）

通过命令：`listunspent [minconf=1] [maxconf=9999999]  ["address",...]`列出某个地址未花费的币(交易)，`minconf`/`maxconf`表示该笔收入交易的确认数范围，如果需要列出还未确认的交易，需将`minconf`设置为0。

执行：

```
bitcoind listunspent 0 100 '["1Lab618UuWjLmVA1Q64tHZXcLoc4397ZX3"]'
```
输出：

```
[
    {
        "txid" : "296ea7bf981b44999d689853d17fe0ceb852a8a34e68fcd19f0a41e589132156",
        "vout" : 0,
        "address" : "1Lab618UuWjLmVA1Q64tHZXcLoc4397ZX3",
        "account" : "",
        "scriptPubKey" : "76a914d6c492056f3f99692b56967a42b8ad44ce76b67a88ac",
        "amount" : 0.19900000,
        "confirmations" : 1
    }
]
```
我们找到该地址的一个未花费交易，位于交易[296ea7bf981b4499...9f0a41e589132156](http://blockchain.info/tx/296ea7bf981b44999d689853d17fe0ceb852a8a34e68fcd19f0a41e589132156)的第0个位置。

#### 2. 创建待发送交易

创建待发送交易，由命令：`createrawtransaction [{"txid":txid,"vout":n},...] {address:amount,...}`来完成。我们将 _0.1_ BTC发送至 _1Q8s4qDRbCbFypG5AFNR9tFC57PStkPX1x_ ，并支付 _0.0001_ BTC做为矿工费。输入交易的额度为 _0.199_ ，输出为 _0.1 + 0.0001 = 0.1001_ ，那么还剩余： _0.199 - 0.1001 = 0.0989_ ，将此作为找零发回给自己。

执行：

```
bitcoind createrawtransaction \
'[{"txid":"296ea7bf981b44999d689853d17fe0ceb852a8a34e68fcd19f0a41e589132156","vout":0}]' \
'{"1Q8s4qDRbCbFypG5AFNR9tFC57PStkPX1x":0.1, "1Lab618UuWjLmVA1Q64tHZXcLoc4397ZX3":0.0989}'
```

输出：

```
010000000156211389e5410a9fd1fc684ea3a852b8cee07fd15398689d99441b98bfa76e290000000000ffffffff0280969800000000001976a914fdc7990956642433ea75cabdcc0a9447c5d2b4ee88acd0e89600000000001976a914d6c492056f3f99692b56967a42b8ad44ce76b67a88ac00000000
```

通过命令：`decoderawtransaction <hex string>`，可以将此段十六进制字符串解码。

执行：

```
bitcoind decoderawtransaction '010000000156211389e5410a9fd1fc684ea3a852b8cee07fd15398689d99441b98bfa76e290000000000ffffffff0280969800000000001976a914fdc7990956642433ea75cabdcc0a9447c5d2b4ee88acd0e89600000000001976a914d6c492056f3f99692b56967a42b8ad44ce76b67a88ac00000000'
```
输出：

```
{
    "txid" : "54f773a3fdf7cb3292fc76b46c97e536348b3a0715886dbfd2f60e115fb3a8f0",
    "version" : 1,
    "locktime" : 0,
    "vin" : [
        {
            "txid" : "296ea7bf981b44999d689853d17fe0ceb852a8a34e68fcd19f0a41e589132156",
            "vout" : 0,
            "scriptSig" : {
                "asm" : "",
                "hex" : ""
            },
            "sequence" : 4294967295
        }
    ],
    "vout" : [
        {
            "value" : 0.10000000,
            "n" : 0,
            "scriptPubKey" : {
                "asm" : "OP_DUP OP_HASH160 fdc7990956642433ea75cabdcc0a9447c5d2b4ee OP_EQUALVERIFY OP_CHECKSIG",
                "hex" : "76a914fdc7990956642433ea75cabdcc0a9447c5d2b4ee88ac",
                "reqSigs" : 1,
                "type" : "pubkeyhash",
                "addresses" : [
                    "1Q8s4qDRbCbFypG5AFNR9tFC57PStkPX1x"
                ]
            }
        },
        {
            "value" : 0.09890000,
            "n" : 1,
            "scriptPubKey" : {
                "asm" : "OP_DUP OP_HASH160 d6c492056f3f99692b56967a42b8ad44ce76b67a OP_EQUALVERIFY OP_CHECKSIG",
                "hex" : "76a914d6c492056f3f99692b56967a42b8ad44ce76b67a88ac",
                "reqSigs" : 1,
                "type" : "pubkeyhash",
                "addresses" : [
                    "1Lab618UuWjLmVA1Q64tHZXcLoc4397ZX3"
                ]
            }
        }
    ]
}
```

至此，一个“空白交易”就构造好了，尚未使用私钥对交易进行签名，字段`scriptSig`是留空的，无签名的交易是无效的。此时的Tx ID并不是最终的Tx ID，填入签名后Tx ID会发生变化。

在手动创建交易时，务必注意输入、输出的值，`非常容易犯错的是忘记构造找零输出`（如非必要勿手动构造交易）。曾经有人构造交易时忘记找零，发生了[支付 **200 BTC** 的矿工费](https://blockchain.info/tx/4ed20e0768124bc67dc684d57941be1482ccdaa45dadb64be12afba8c8554537)的人间惨剧，所幸的是收录该笔交易的Block由著名挖矿团队“烤猫（Friedcat）”挖得，该团队非常厚道的[退回了多余费用](https://blockchain.info/tx/b18abce37b48a5f434f108ae7ce34f22aa2bfbd9eb9310314029e4b9e3c7cf95)。

#### 3. 签名

交易签名使用命令：

```
signrawtransaction <hex string> \
[{"txid":txid,"vout":n,"scriptPubKey":hex,"redeemScript":hex},...] [<privatekey1>,...] \
[sighashtype="ALL"]
```

 * 第一个参数是创建的待签名交易的十六进制字符串；
 * 第二个参数有点类似创建交易时的参数，不过需要多出一个公钥字段`scriptPubKey`，其他节点验证交易时是通过公钥和签名来完成的，所以要提供公钥；如果是合成地址，则需要提供`redeemScript`；
 * 第三个参数是即将花费的币所在地址的私钥，用来对交易进行签名，如果该地址私钥已经导入至bitcoind中，则无需显式提供；
 * 最后一个参数表示签名类型，在上一篇里，介绍了三种交易签名类型；

签名之前需要找到`scriptPubKey`，提取输入交易信息即可获取(也可以根据其公钥自行计算)，由命令：`getrawtransaction <txid> [verbose=0]`完成。

执行：

```
bitcoind getrawtransaction 296ea7bf981b44999d689853d17fe0ceb852a8a34e68fcd19f0a41e589132156 1
```
输出：

```
{
    "hex" : "01000000010511331f639e974283d3909496787a660583dc88f41598d177e225b5f352314a000000006c493046022100be8c796122ec598295e6dfd6664a20a7e20704a17f76d3d925c9ec421ca60bc1022100cf9f2d7b9f24285f7c119c91f24521e5483f6b141de6ee55658fa70116ee04d4012103cad07f6de0b181891b5291a5bc82b228fe6509699648b0b53556dc0057eeb5a4ffffffff0160a62f01000000001976a914d6c492056f3f99692b56967a42b8ad44ce76b67a88ac00000000",
    "txid" : "296ea7bf981b44999d689853d17fe0ceb852a8a34e68fcd19f0a41e589132156",
    "version" : 1,
    "locktime" : 0,
    "vin" : [
        {
            "txid" : "4a3152f3b525e277d19815f488dc8305667a78969490d38342979e631f331105",
            "vout" : 0,
            "scriptSig" : {
                "asm" : "3046022100be8c796122ec598295e6dfd6664a20a7e20704a17f76d3d925c9ec421ca60bc1022100cf9f2d7b9f24285f7c119c91f24521e5483f6b141de6ee55658fa70116ee04d401 03cad07f6de0b181891b5291a5bc82b228fe6509699648b0b53556dc0057eeb5a4",
                "hex" : "493046022100be8c796122ec598295e6dfd6664a20a7e20704a17f76d3d925c9ec421ca60bc1022100cf9f2d7b9f24285f7c119c91f24521e5483f6b141de6ee55658fa70116ee04d4012103cad07f6de0b181891b5291a5bc82b228fe6509699648b0b53556dc0057eeb5a4"
            },
            "sequence" : 4294967295
        }
    ],
    "vout" : [
        {
            "value" : 0.19900000,
            "n" : 0,
            "scriptPubKey" : {
                "asm" : "OP_DUP OP_HASH160 d6c492056f3f99692b56967a42b8ad44ce76b67a OP_EQUALVERIFY OP_CHECKSIG",
                "hex" : "76a914d6c492056f3f99692b56967a42b8ad44ce76b67a88ac",
                "reqSigs" : 1,
                "type" : "pubkeyhash",
                "addresses" : [
                    "1Lab618UuWjLmVA1Q64tHZXcLoc4397ZX3"
                ]
            }
        }
    ],
    "blockhash" : "000000000000000488f18f7659acd85b2bd06a5ed2c4439eea74a8b968d16656",
    "confirmations" : 19,
    "time" : 1383235737,
    "blocktime" : 1383235737
}
```

`scriptPubKey`位于"vout"[0]->"scriptPubKey"->"hex"，即： _76a914d6c492056f3f99692b56967a42b8ad44ce76b67a88ac_ 。

签名使用ECDSA算法，对其，“空白交易”签名之，执行：

```
bitcoind signrawtransaction \
"010000000156211389e5410a9fd1fc684ea3a852b8cee07fd15398689d99441b98bfa76e290000000000ffffffff0280969800000000001976a914fdc7990956642433ea75cabdcc0a9447c5d2b4ee88acd0e89600000000001976a914d6c492056f3f99692b56967a42b8ad44ce76b67a88ac00000000" \
'[{"txid":"296ea7bf981b44999d689853d17fe0ceb852a8a34e68fcd19f0a41e589132156","vout":0,"scriptPubKey":"76a914d6c492056f3f99692b56967a42b8ad44ce76b67a88ac"}]'
```
输出：

```
{
    "hex" : "010000000156211389e5410a9fd1fc684ea3a852b8cee07fd15398689d99441b98bfa76e29000000008c493046022100f9da4f53a6a4a8317f6e7e9cd9a7b76e0f5e95dcdf70f1b1e2b3548eaa3a6975022100858d48aed79da8873e09b0e41691f7f3e518ce9a88ea3d03f7b32eb818f6068801410477c075474b6798c6e2254d3d06c1ae3b91318ca5cc62d18398697208549f798e28efb6c55971a1de68cca81215dd53686c31ad8155cdc03563bf3f73ce87b4aaffffffff0280969800000000001976a914fdc7990956642433ea75cabdcc0a9447c5d2b4ee88acd0e89600000000001976a914d6c492056f3f99692b56967a42b8ad44ce76b67a88ac00000000",
    "complete" : true
}
```

签名后，签名值会填入上文所述的空字段中，从而得到一个完整的交易。可通过上文介绍的命令`decoderawtransaction <hex string>`解码查看之。

最后一步，就是将其广播出去，等待网络传播至所有节点，约10~60秒广播至全球节点，取决与你的节点的网络连接状况。稍后一些时刻，就会进入Block中。广播由命令`sendrawtransaction <hex string>`来完成。如果没有运行节点，可以通过公共节点的API进行广播，例如：[blockchain.info/pushtx](https://blockchain.info/pushtx)。

执行：

```
bitcoind sendrawtransaction \
"010000000156211389e5410a9fd1fc684ea3a852b8cee07fd15398689d99441b98bfa76e29000000008c493046022100f9da4f53a6a4a8317f6e7e9cd9a7b76e0f5e95dcdf70f1b1e2b3548eaa3a6975022100858d48aed79da8873e09b0e41691f7f3e518ce9a88ea3d03f7b32eb818f6068801410477c075474b6798c6e2254d3d06c1ae3b91318ca5cc62d18398697208549f798e28efb6c55971a1de68cca81215dd53686c31ad8155cdc03563bf3f73ce87b4aaffffffff0280969800000000001976a914fdc7990956642433ea75cabdcc0a9447c5d2b4ee88acd0e89600000000001976a914d6c492056f3f99692b56967a42b8ad44ce76b67a88ac00000000"
```

输出：

```
b5f8da1ea9e02ec3cc0765f9600f49945e94ed4b0c88ed0648896bf3e213205d
```

返回的是Transaction Hash值，即[该交易](https://blockchain.info/tx/b5f8da1ea9e02ec3cc0765f9600f49945e94ed4b0c88ed0648896bf3e213205d)的ID。至此，交易构造、签名、发送的完整过程完成了。

### 合成地址交易

合成地址以3开头，可以实现多方管理资产，极大提高安全性，也可以轻松实现基于比特币原生的三方交易担保支付。一个`M-of-N`的模式：

```
m {pubkey}...{pubkey} n OP_CHECKMULTISIG
```
M和N需满足：

 * `1<=N<=3`
 * `1<=M<=N`

可以是`1 of 1`，`1 of 2`，`2 of 3`等组合，通常选择`N=3`：

 * `1 of 3`，最大程度私钥冗余。防丢私钥损失，3把私钥中任意一把即可签名发币，即使丢失2把都可以保障不受损失；
 * `2 of 3`，提高私钥冗余度的同时解决单点信任问题。3把私钥任意2把私钥可签名发币，三方不完全信任的情形，即中介交易中，非常适用；
 * `3 of 3`，最大程度解决资金信任问题，无私钥冗余。必须3把私钥全部签名才能发币，适用多方共同管理重要资产，但任何一方遗失私钥均造成严重损失；

合成地址的交易构造、签名、发送过程与普通交易类似，这里只介绍如何创建一个合成地址。大神Gavin Andresen已经演示过，下面内容摘自其[gist](https://gist.github.com/gavinandresen/3966071).

首先，需要三对公钥、私钥。公钥创建地址、私钥用于签名。

```
# No.1
0491bba2510912a5bd37da1fb5b1673010e43d2c6d812c514e91bfa9f2eb129e1c183329db55bd868e209aac2fbc02cb33d98fe74bf23f0c235d6126b1d8334f86 / 5JaTXbAUmfPYZFRwrYaALK48fN6sFJp4rHqq2QSXs8ucfpE4yQU
# No.2 
04865c40293a680cb9c020e7b1e106d8c1916d3cef99aa431a56d253e69256dac09ef122b1a986818a7cb624532f062c1d1f8722084861c5c3291ccffef4ec6874 / 5Jb7fCeh1Wtm4yBBg3q3XbT6B525i17kVhy3vMC9AqfR6FH2qGk
# No.3
048d2455d2403e08708fc1f556002f1b6cd83f992d085097f9974ab08a28838f07896fbab08f39495e15fa6fad6edbfb1e754e35fa1c7844c41f322a1863d46213 / 5JFjmGo5Fww9p8gvx48qBYDJNAzR9pmH5S389axMtDyPT8ddqmw
```

使用命令：`createmultisig <nrequired> <'["key","key"]'>`来合成，其中`key`为公钥，创建地址时仅需公钥。创建类型是`2 of 3`.

输入：

```
bitcoind createmultisig 2 \
'["0491bba2510912a5bd37da1fb5b1673010e43d2c6d812c514e91bfa9f2eb129e1c183329db55bd868e209aac2fbc02cb33d98fe74bf23f0c235d6126b1d8334f86","04865c40293a680cb9c020e7b1e106d8c1916d3cef99aa431a56d253e69256dac09ef122b1a986818a7cb624532f062c1d1f8722084861c5c3291ccffef4ec6874","048d2455d2403e08708fc1f556002f1b6cd83f992d085097f9974ab08a28838f07896fbab08f39495e15fa6fad6edbfb1e754e35fa1c7844c41f322a1863d46213"]'
```

输出：

```
{
    "address" : "3QJmV3qfvL9SuYo34YihAf3sRCW3qSinyC",
    "redeemScript" : "52410491bba2510912a5bd37da1fb5b1673010e43d2c6d812c514e91bfa9f2eb129e1c183329db55bd868e209aac2fbc02cb33d98fe74bf23f0c235d6126b1d8334f864104865c40293a680cb9c020e7b1e106d8c1916d3cef99aa431a56d253e69256dac09ef122b1a986818a7cb624532f062c1d1f8722084861c5c3291ccffef4ec687441048d2455d2403e08708fc1f556002f1b6cd83f992d085097f9974ab08a28838f07896fbab08f39495e15fa6fad6edbfb1e754e35fa1c7844c41f322a1863d4621353ae"
}
```

得到的合成地址是：`3QJmV3qfvL9SuYo34YihAf3sRCW3qSinyC`，该地址没有公钥，仅有`redeemScript`，作用与公钥相同。后续的构造、签名、发送过程与上文普通地址交易类似，略去。
