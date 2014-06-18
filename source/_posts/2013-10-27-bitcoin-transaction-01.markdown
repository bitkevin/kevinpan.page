---
layout: post
title: "比特币交易构成（一）"
date: 2013-10-27 19:42
comments: true
categories: 
---

## 简介
交易(Transaction)是比特币系统的信息载体，最小单元。而块(Block)就是将这些基础单元打包装箱，贴上封条，并串联起来。巨大算力保障了块的安全，也就保障了单个交易的安全。


## 类型

交易有三种常见类型：产量交易(Generation)，合成地址交易(Script Hash)，通用地址交易(Pubkey Hash)。该分类并非严格意义的，只是根据交易的输入输出做的简单区分。

### Generation TX

每个Block都对应一个产量交易(Generation TX)，该类交易是没有输入交易的，挖出的新币是所有币的源头。

### Script Hash TX

该类交易目前不是很常见，大部分人可能没有听说过，但是非常有意义。未来应该会在某些场合频繁使用。该类交易的接受地址不是通常意义的地址，而是一个合成地址，以3开头（对，以3开头的也是比特币地址！）。三对公私钥，可以生成一个合成地址。在生成过程时指定`n of 3`中的n，n范围是`[1, 3]`，若n=1，则仅需一个私钥签名即可花费该地址的币，若n=3，则需要三把私钥依次签名才可以。

### Pubkey Hash TX

该类是最常见的交易类型，由N个输入、M个输出构成。

## 数据结构

交易中存放的是货币所有权的流转信息，所有权登记在比特币地址上(Public Key)。这些信息是全网公开的，以明文形式存储（比特币系统里的所有数据都是明文的），只有当需要转移货币所有权时，才需要用私钥签名来验证。

字段大小 | 描述 | 数据类型 | 解释
---------|------|--------|-------
4 | version, 版本 | uint32_t | 交易数据结构的版本号
1+ | tx_in count, 输入数量 | var_int | 输入交易的数量
41+ | tx_in | tx_in[] | 输入交易的数组，每个输入>=41字节
1+ | tx_out count, 输出数量 | var_int | 输出地址的数量
9+ | tx_out | tx_out[] | 输入地址的数组，每个输入>=9字节
4 | lock_time, 锁定时间 | uint32_t | 见下方解释

`lock_time`是一个多意字段，表示在某个高度的Block之前或某个时间点之前该交易处于锁定态，无法收录进Block。

值 | 含义
------|-------------
0 | 立即生效
&lt; 500000000 | 含义为Block高度，处于该Block之前为锁定（不生效）
&gt;= 500000000 | 含义为Unix时间戳，处于该时刻之前为锁定（不生效）

若该笔交易的所有输入交易的`sequence`字段，均为INT32最大值(0xffffffff)，则忽略`lock_time`字段。否则，该交易在未达到Block高度或达到某个时刻之前，是不会被收录进Block中的。


### 示例

为了演示方便，我们读取稍早期的块数据，以高度116219 Block为例。

```
# ~  bitcoind getblock 0000000000007c639f2cbb23e4606a1d022fa4206353b9d92e99f5144bd74611           
{
    "hash" : "0000000000007c639f2cbb23e4606a1d022fa4206353b9d92e99f5144bd74611",
    "confirmations" : 144667,
    "size" : 1536,
    "height" : 116219,
    "version" : 1,
    "merkleroot" : "587fefd748f899f84d0fa1d8a3876fdb406a4bb8f54a31445cb72564701daea6",
    "tx" : [
        "be8f08d7f519eb863a68cf292ca51dbab7c9b49f50a96d13f2db32e432db363e",
        "a387039eca66297ba51ef2da3dcc8a0fc745bcb511e20ed9505cc6762be037bb",
        "2bd83162e264abf59f9124ca517050065f8c8eed2a21fbf85d454ee4e0e4c267",
        "028cfae228f8a4b0caee9c566bd41aed36bcd237cdc0eb18f0331d1e87111743",
        "3a06b6615756dc3363a8567fbfa8fe978ee0ba06eb33fd844886a0f01149ad62"
    ],
    "time" : 1301705313,
    "nonce" : 1826107553,
    "bits" : "1b00f339",
    "difficulty" : 68977.78463021,
    "previousblockhash" : "00000000000010d549135eb39bd3bbb1047df8e1512357216e8a85c57a1efbfb",
    "nextblockhash" : "000000000000e9fcc59a6850f64a94476a30f5fe35d6d8c4b4ce0b1b04103a77"
}
```

该Block里面有5笔交易，第一笔为Generation TX，解析出来看一下具体内容：

```
# ~  bitcoind getrawtransaction be8f08d7f519eb863a68cf292ca51dbab7c9b49f50a96d13f2db32e432db363e 1
{
    "hex" : "01000000010000000000000000000000000000000000000000000000000000000000000000ffffffff070439f3001b0134ffffffff014034152a010000004341045b3aaa284d169c5ae2d20d0b0673468ed3506aa8fea5976eacaf1ff304456f6522fbce1a646a24005b8b8e771a671f564ca6c03e484a1c394bf96e2a4ad01dceac00000000",
    "txid" : "be8f08d7f519eb863a68cf292ca51dbab7c9b49f50a96d13f2db32e432db363e",
    "version" : 1,
    "locktime" : 0,
    "vin" : [
        {
            "coinbase" : "0439f3001b0134",
            "sequence" : 4294967295
        }
    ],
    "vout" : [
        {
            "value" : 50.01000000,
            "n" : 0,
            "scriptPubKey" : {
                "asm" : "045b3aaa284d169c5ae2d20d0b0673468ed3506aa8fea5976eacaf1ff304456f6522fbce1a646a24005b8b8e771a671f564ca6c03e484a1c394bf96e2a4ad01dce OP_CHECKSIG",
                "hex" : "41045b3aaa284d169c5ae2d20d0b0673468ed3506aa8fea5976eacaf1ff304456f6522fbce1a646a24005b8b8e771a671f564ca6c03e484a1c394bf96e2a4ad01dceac",
                "reqSigs" : 1,
                "type" : "pubkey",
                "addresses" : [
                    "1LgZTvoTJ6quJNCURmBUaJJkWWQZXkQnDn"
                ]
            }
        }
    ],
    "blockhash" : "0000000000007c639f2cbb23e4606a1d022fa4206353b9d92e99f5144bd74611",
    "confirmations" : 145029,
    "time" : 1301705313,
    "blocktime" : 1301705313
}
```

Generation TX的输入不是一个交易，而带有`coinbase`字段的结构。该字段的值由挖出此Block的人填写，这是一种“特权”：可以把信息写入货币系统（大家很喜欢用系统中的数据结构字段名来命名站点，例如blockchain、coinbase等，这些词的各种后缀域名都被抢注一空）。中本聪在比特币的第一个交易中的写入的`coinbase`值是：

```
"coinbase":"04ffff001d0104455468652054696d65732030332f4a616e2f32303039204368616e63656c6c6f72206f6e206272696e6b206f66207365636f6e64206261696c6f757420666f722062616e6b73"
```
将该段16进制转换为ASCII字符，就是那段著名的创世块留言：

```
The Times 03/Jan/2009 Chancellor on brink of second bailout for banks
```

接下来展示的是一个三个输入、两个输出的普通交易：

```
# ~  bitcoind getrawtransaction 028cfae228f8a4b0caee9c566bd41aed36bcd237cdc0eb18f0331d1e87111743 1
{
    "hex" : "0100000003c9f3b07ebfca68fd1a6339d0808fbb013c90c6095fc93901ea77410103489ab7000000008a473044022055bac1856ecbc377dd5e869b1a84ed1d5228c987b098c095030c12431a4d5249022055523130a9d0af5fc27828aba43b464ecb1991172ba2a509b5fbd6cac97ff3af0141048aefd78bba80e2d1686225b755dacea890c9ca1be10ec98173d7d5f2fefbbf881a6e918f3b051f8aaaa3fcc18bbf65097ce8d30d5a7e5ef8d1005eaafd4b3fbeffffffffc9f3b07ebfca68fd1a6339d0808fbb013c90c6095fc93901ea77410103489ab7010000008a47304402206b993231adec55e6085e75f7dc5ca6c19e42e744cd60abaff957b1c352b3ef9a022022a22fec37dfa2c646c78d9a0753d56cb4393e8d0b22dc580ef1aa6cccef208d0141042ff65bd6b3ef04253225405ccc3ab2dd926ff2ee48aac210819698440f35d785ec3cec92a51330eb0c76cf49e9e474fb9159ab41653a9c1725c031449d31026affffffffc98620a6c40fc7b3a506ad79af339541762facd1dd80ff0881d773fb72b230da010000008b483045022040a5d957e087ed61e80f1110bcaf4901b5317c257711a6cbc54d6b98b6a8563f02210081e3697031fe82774b8f44dd3660901e61ac5a99bff2d0efc83ad261da5b4f1d014104a7d1a57e650613d3414ebd59e3192229dc09d3613e547bdd1f83435cc4ca0a11c679d96456cae75b1f5563728ec7da1c1f42606db15bf554dbe8a829f3a8fe2fffffffff0200bd0105000000001976a914634228c26cf40a02a05db93f2f98b768a8e0e61b88acc096c7a6030000001976a9147514080ab2fcac0764de3a77d10cb790c71c74c288ac00000000",
    "txid" : "028cfae228f8a4b0caee9c566bd41aed36bcd237cdc0eb18f0331d1e87111743",
    "version" : 1,
    "locktime" : 0,
    "vin" : [
        {
            "txid" : "b79a4803014177ea0139c95f09c6903c01bb8f80d039631afd68cabf7eb0f3c9",
            "vout" : 0,
            "scriptSig" : {
                "asm" : "3044022055bac1856ecbc377dd5e869b1a84ed1d5228c987b098c095030c12431a4d5249022055523130a9d0af5fc27828aba43b464ecb1991172ba2a509b5fbd6cac97ff3af01 048aefd78bba80e2d1686225b755dacea890c9ca1be10ec98173d7d5f2fefbbf881a6e918f3b051f8aaaa3fcc18bbf65097ce8d30d5a7e5ef8d1005eaafd4b3fbe",
                "hex" : "473044022055bac1856ecbc377dd5e869b1a84ed1d5228c987b098c095030c12431a4d5249022055523130a9d0af5fc27828aba43b464ecb1991172ba2a509b5fbd6cac97ff3af0141048aefd78bba80e2d1686225b755dacea890c9ca1be10ec98173d7d5f2fefbbf881a6e918f3b051f8aaaa3fcc18bbf65097ce8d30d5a7e5ef8d1005eaafd4b3fbe"
            },
            "sequence" : 4294967295
        },
        {
            "txid" : "b79a4803014177ea0139c95f09c6903c01bb8f80d039631afd68cabf7eb0f3c9",
            "vout" : 1,
            "scriptSig" : {
                "asm" : "304402206b993231adec55e6085e75f7dc5ca6c19e42e744cd60abaff957b1c352b3ef9a022022a22fec37dfa2c646c78d9a0753d56cb4393e8d0b22dc580ef1aa6cccef208d01 042ff65bd6b3ef04253225405ccc3ab2dd926ff2ee48aac210819698440f35d785ec3cec92a51330eb0c76cf49e9e474fb9159ab41653a9c1725c031449d31026a",
                "hex" : "47304402206b993231adec55e6085e75f7dc5ca6c19e42e744cd60abaff957b1c352b3ef9a022022a22fec37dfa2c646c78d9a0753d56cb4393e8d0b22dc580ef1aa6cccef208d0141042ff65bd6b3ef04253225405ccc3ab2dd926ff2ee48aac210819698440f35d785ec3cec92a51330eb0c76cf49e9e474fb9159ab41653a9c1725c031449d31026a"
            },
            "sequence" : 4294967295
        },
        {
            "txid" : "da30b272fb73d78108ff80ddd1ac2f76419533af79ad06a5b3c70fc4a62086c9",
            "vout" : 1,
            "scriptSig" : {
                "asm" : "3045022040a5d957e087ed61e80f1110bcaf4901b5317c257711a6cbc54d6b98b6a8563f02210081e3697031fe82774b8f44dd3660901e61ac5a99bff2d0efc83ad261da5b4f1d01 04a7d1a57e650613d3414ebd59e3192229dc09d3613e547bdd1f83435cc4ca0a11c679d96456cae75b1f5563728ec7da1c1f42606db15bf554dbe8a829f3a8fe2f",
                "hex" : "483045022040a5d957e087ed61e80f1110bcaf4901b5317c257711a6cbc54d6b98b6a8563f02210081e3697031fe82774b8f44dd3660901e61ac5a99bff2d0efc83ad261da5b4f1d014104a7d1a57e650613d3414ebd59e3192229dc09d3613e547bdd1f83435cc4ca0a11c679d96456cae75b1f5563728ec7da1c1f42606db15bf554dbe8a829f3a8fe2f"
            },
            "sequence" : 4294967295
        }
    ],
    "vout" : [
        {
            "value" : 0.84000000,
            "n" : 0,
            "scriptPubKey" : {
                "asm" : "OP_DUP OP_HASH160 634228c26cf40a02a05db93f2f98b768a8e0e61b OP_EQUALVERIFY OP_CHECKSIG",
                "hex" : "76a914634228c26cf40a02a05db93f2f98b768a8e0e61b88ac",
                "reqSigs" : 1,
                "type" : "pubkeyhash",
                "addresses" : [
                    "1A3q9pDtR4h8wpvyb8SVpiNPpT8ZNbHY8h"
                ]
            }
        },
        {
            "value" : 156.83000000,
            "n" : 1,
            "scriptPubKey" : {
                "asm" : "OP_DUP OP_HASH160 7514080ab2fcac0764de3a77d10cb790c71c74c2 OP_EQUALVERIFY OP_CHECKSIG",
                "hex" : "76a9147514080ab2fcac0764de3a77d10cb790c71c74c288ac",
                "reqSigs" : 1,
                "type" : "pubkeyhash",
                "addresses" : [
                    "1Bg44FZsoTeYteRykC1XHz8facWYKhGvQ8"
                ]
            }
        }
    ],
    "blockhash" : "0000000000007c639f2cbb23e4606a1d022fa4206353b9d92e99f5144bd74611",
    "confirmations" : 147751,
    "time" : 1301705313,
    "blocktime" : 1301705313
}
```

字段`hex`记录了所有相关信息，后面显示的是`hex`解析出来的各类字段信息。下面把逐个分解`hex`内容（hex可以从上面的直接看到）：

```
01000000   // 版本号，UINT32
03         // Tx输入数量，变长INT。3个输入。

/*** 第一组Input Tx ***/
// Tx Hash，固定32字节
c9f3b07ebfca68fd1a6339d0808fbb013c90c6095fc93901ea77410103489ab7
00000000  // 消费的Tx位于前向交易输出的第0个，UINT32，固定4字节
8a        // 签名的长度, 0x8A = 138字节
// 138字节长度的签名，含有两个部分：公钥+签名
47       // 签名长度，0x47 = 71字节
3044022055bac1856ecbc377dd5e869b1a84ed1d5228c987b098c095030c12431a4d5249022055523130a9d0af5fc27828aba43b464ecb1991172ba2a509b5fbd6cac97ff3af01
41       // 公钥长度，0x41 = 65字节
048aefd78bba80e2d1686225b755dacea890c9ca1be10ec98173d7d5f2fefbbf881a6e918f3b051f8aaaa3fcc18bbf65097ce8d30d5a7e5ef8d1005eaafd4b3fbe
ffffffff  // sequence，0xffffffff = 4294967295， UINT32, 固定4字节

/*** 第二组Input Tx。与上同理，省略分解 ***/
c9f3b07ebfca68fd1a6339d0808fbb013c90c6095fc93901ea77410103489ab7010000008a47304402206b993231adec55e6085e75f7dc5ca6c19e42e744cd60abaff957b1c352b3ef9a022022a22fec37dfa2c646c78d9a0753d56cb4393e8d0b22dc580ef1aa6cccef208d0141042ff65bd6b3ef04253225405ccc3ab2dd926ff2ee48aac210819698440f35d785ec3cec92a51330eb0c76cf49e9e474fb9159ab41653a9c1725c031449d31026affffffff

/*** 第三组Input Tx ***/
c98620a6c40fc7b3a506ad79af339541762facd1dd80ff0881d773fb72b230da010000008b483045022040a5d957e087ed61e80f1110bcaf4901b5317c257711a6cbc54d6b98b6a8563f02210081e3697031fe82774b8f44dd3660901e61ac5a99bff2d0efc83ad261da5b4f1d014104a7d1a57e650613d3414ebd59e3192229dc09d3613e547bdd1f83435cc4ca0a11c679d96456cae75b1f5563728ec7da1c1f42606db15bf554dbe8a829f3a8fe2fffffffff

02  // Tx输出数量，变长INT。两个输出。

/*** 第一组输出 ***/
00bd010500000000    // 输出的币值，UINT64，8个字节。字节序需翻转，~= 0x000000000501bd00 = 84000000 satoshi
19                  // 输出目的地址字节数, 0x19 = 25字节，由一些操作码与数值构成
// 目标地址
// 0x76 -> OP_DUP(stack ops)
// 0xa9 -> OP_HASH160(crypto)
// 0x14 -> 长度，0x14 = 20字节
76 a9 14 
// 地址的HASH160值，20字节
634228c26cf40a02a05db93f2f98b768a8e0e61b 
// 0x88 -> OP_EQUALVERIFY(bit logic)
// 0xac -> OP_CHECKSIG(crypto)
88 ac

/*** 第二组输出 ***/
c096c7a603000000
19
76 a9 14 7514080ab2fcac0764de3a77d10cb790c71c74c2 88 ac

00000000  // lock_time，UINT32，固定4字节
```

Tx Hash，俗称交易ID，由`hex`得出：`Tx Hash = SHA256(SHA256(hex))`。由于每个交易只能成为下一个的输入，有且仅有一次，那么不存在输入完全相同的交易，那么就不存在相同的Tx Hash（SHA256碰撞概率极小，所以无需考虑Hash碰撞的问题，就像无需考虑地址私钥被别人撞到一样）。

即便如此，在系统里依然产生了相同的Tx Hash，是某位矿工兄弟挖出Block后，打包Block时忘记修改Generation Tx coinbase字段的值，币量相同且输出至相同的地址，那么就构造了两个完全一模一样的交易，分别位于两个Block的第一个位置。这个对系统不会产生什么问题，但只要花费其中一笔，另一个也被花费了。相同的Generation Tx相当于覆盖了另一个，白白损失了挖出的币。该交易ID为[e3bf3d07d4b0375638d5f1db5255fe07ba2c4cb067cd81b84ee974b6585fb468](https://blockchain.info/tx/e3bf3d07d4b0375638d5f1db5255fe07ba2c4cb067cd81b84ee974b6585fb468 "e3bf3d07d4b0375638d5f1db5255fe07ba2c4cb067cd81b84ee974b6585fb468")，第一次出现在[#91722](https://blockchain.info/block/00000000000271a2dc26e7667f8419f2e15416dc6955e5a6c6cdf3f2574dd08e)，第二次出现在[#91880](https://blockchain.info/block/00000000000743f190a18c5577a3c2d2a1f610ae9601ac046a38084ccb7cd721)。

![qq20131027-2](https://f.cloud.github.com/assets/514951/1415138/b01dc7da-3edf-11e3-86eb-d015037e9440.png)


## 交易签名

签名是对所有权的验证，节点收到交易广播后，会对交易进行验证，通过后则收录进内存、打包进Block，否则，丢弃之。签名就类似传统纸质合同盖章、签字过程，合法转移所有权的保证手段。

### 签名类型

由于一个交易的输入、输出都可能具有多个，那么签名也具有多种类型，目前共三类：SIGHASH_ALL, SIGHASH_NONE, SIGHASH_SINGLE。

#### SIGHASH_ALL

该签名类型为默认类型，也是目前绝大部分交易采用的，顾名思义即签名整单交易。首先，组织所有输出、输入，就像上文分解Hex过程一样，每个输入都对应一个签名，暂时留空，其他包括sequence等字段均须填写，这样就形成了一个完整的交易Hex（只缺签名字段）。然后，每一个输入均需使用私钥对该段数据进行签名，签名完成后各自填入相应的位置，N个输入N个签名。简单理解就是：对于该笔单子，认可且只认可的这些输入、输出，并同意花费我的那笔输入。

#### SIGHASH_NONE

该签名类型是最自由松散的，仅对输入签名，不对输出签名，输出可以任意指定。某人对某笔币签名后交给你，你可以在任意时刻填入任意接受地址，广播出去令其生效。简单理解就是：我同意花费我的那笔钱，至于给谁，我不关心。

#### SIGHASH_SINGLE

该签名类型其次自由松散，仅对自己的输入、输出签名，并留空sequence字段。其输入的次序对应其输出的次序，比如输入是第3个，那么签名的输出也是第三个。简单理解就是：我同意花费我的那笔钱，且只能花费到我认可的输出，至于单子里的其他输入、输出，我不关心。

