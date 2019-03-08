title: Schnorr签名介绍
date: 2019-02-28 13:24:52

---

Schnorr签名算法是由德国数学家、密码学家Claus Schnorr提出。并于1990年申请了专利，[U.S. Patent 4,995,082](https://www.google.com/patents/US4995082)，该专利与2008年2月失效。目前该算法可以自由使用。

Schnorr签名算法几乎在各个层面均优于比特币现有的签名算法ECDSA：性能，安全，体积，扩展性等方面。

Schnorr Sig可以与ECDSA使用同一个椭圆曲线：[secp256k1 curve](http://www.secg.org/sec2-v2.pdf)，升级起来的改动非常小。

## 原理

我们定义几个变量：

* G：椭圆曲线。
* m：待签名的数据，通常是一个32字节的哈希值。
* x：私钥。`P = xG`，P为x对应的公钥。
* H()：哈希函数。
  * 示例：写法`H(m || R || P)`可理解为：将m, R, P三个字段拼接在一起然后再做哈希运算。

### 生成签名

签名者已知的是：G-椭圆曲线, H()-哈希函数，m-待签名消息, x-私钥。

1. 选择一个随机数`k`, 令 `R = kG`
2. 令 `s = k + H(m || R || P)*x`

那么，公钥P对消息m的签名就是：`(R, s)`，这一对值即为Schnorr签名。


### 验证签名

验证者已知的是：G-椭圆曲线, H()-哈希函数，m-待签名消息, P-公钥，(R, s)-Schnorr签名。验证如下等式：

`sG = R + H(m || R || P)P`

若等式成立，则可证明签名合法。

我们推演一下，此过程包含了一个极其重要的理论：椭圆曲线无法进行除法运算。

1. s值的定义：`s = k + H(m || R || P)*x`，等式两边都乘以椭圆曲线G，则有：
1. `sG = kG + H(m || R || P)*x*G`，又因`R = kG, P = xG`，则有：
1. `sG = R + H(m || R || P)P`，椭圆曲线无法进行除法运算，所以第3步的等式，无法向前反推出第1步，就不会暴露k值以及x私钥。同时，也完成了等式验证。

### 组签, Group Signature

一组公钥，N把，签名后得到N个签名。这个N个签名是可以相加的，最终得到一个签名。这个签名的验证通过，则代表N把公钥的签名全部验证通过。

有：
* 椭圆曲线：G
* 待签名的数据：m
* 哈希函数：H()
* 私钥：x1，x2，公钥：P1=x1\*G, P2=x2\*G
* 随机数：k1, k2，并有 R1=k1\*G, R2=k2\*G
* 组公钥：P = P1 + P2

则有：
* 私钥x1和x2的签名为：(R1, s1), (R2, s2)。
* 两个签名相加得到组签名：(R, s)。其中：`R = R1 + R2, s = s1 + s2`。

推演过程：
```
1. 令 R = R1 + R2, s = s1 + s2

2. 已知：s1 = k1 + H(m || R || P)*x1，s2 = k2 + H(m || R || P)*x2

3. s = s1 + s2
     = k1 + H(m || R || P)*x1 +
       k2 + H(m || R || P)*x2
     = (k1 + k2) + H(m || R || P)(x1 + x2)

4. 两边同时乘以G，则有：
    sG = (k1  + k2)G + H(m || R || P)(x1  + x2)G
       = (k1G + k2G) + H(m || R || P)(x1G + x2G)
       = (R1 + R2) + H(m || R || P)(P1 + P2)
       = R + H(m || R || P)P

5. 完成证明，并从两个合作方推演至N个合作方
```

组公钥(Group Key)，是N把公钥进行相加后的值，又称聚合公钥(Aggregation Key)。需要指出的是，参与方需要先相互交换公钥和R值，然后再进行各自的签名。


## 应用

若使用在比特币上，相比ECDSA会有一些额外的显著优势：

* 更安全。目前Schnorr签名有[安全证明](https://www.di.ens.fr/david.pointcheval/Documents/Papers/2000_joc.pdf)，而ECDSA目前并没有类似的证明。
* 无延展性困扰。ECDSA签名是可延展性的，第三方无需知道私钥，可以直接修改既有签名，依然能够保持该签名对于此交易是有效的。比特币一直存在延展性攻击，直到SegWit激活后才修复，前提是使用segwit交易，而不是传统交易。[BIP62](https://github.com/bitcoin/bips/blob/master/bip-0062.mediawiki) 和 [BIP66](https://github.com/bitcoin/bips/blob/master/bip-0066.mediawiki) 对此有详细描述。
* 线性。Schnorr签名算法是线性的！这点非常牛逼，基于这点可衍生出许多应用。例如，N个公钥进行签名，采用ECDSA的话，则有N个签名，验证同样需要做N次。若使用Schnorr，由于线性特性，则可以进行签名叠加，仅保留最终的叠加签名。例如同一个交易无论输入数量多少，其均可叠加为一个签名，一次验证即可。以及GMaxwell提出的Taproot/Grafroot也是基于其线性特性。

## Q&A

Q: Schnorr签名是否可以用在m of n多重签名上？
A: 当然可以。多重签名只是m of n的签名数量的模式。与签名算法无关。

Q: Schnorr的组签名特性是否可以做或模拟出m of n式的签名？
A: 无法做到。组内有N把公钥，则必须对应有N个签名，缺一不可。每个人在生成签名的时候，在哈希函数里都代入的都是组公钥P。

Q: 签名机制的安全性如何衡量？
A: 主要取决于两个：1. 签名算法本身 2. 椭圆曲线。目前，Schnorr与ECDSA都用的是曲线secp256k1，这个层面一样。至于签名算法本身安全性，Schnorr目前有安全证明，安全优于ECDSA。

---

参考：

* Schnorr signature，https://en.wikipedia.org/wiki/Schnorr_signature

* BIP-Schnorr,Pieter Wuille，https://github.com/sipa/bips/blob/bip-schnorr/bip-schnorr.mediawiki
* Simple Schnorr Multi-Signatures with Applications to Bitcoin，https://eprint.iacr.org/2018/068