title: 随机数对签名的重要性与伪签名的构造
date: 2019-03-13 15:44:23

---

关于签名算法的一些基本变量定义：

* G为椭圆曲线
* 随机值k，R=kG
* m，待签名消息哈希值
* 私钥x，公钥P=xG
* H()，哈希函数
* r = R.x，表示r为R的x坐标值

## Schnorr

* 签名：(R, s)
* 生成签名：`s = k + H(R||P||m)*x`
* 验证签名：`sG = R + H(R||P||m)P`

## ECDSA

* 签名：(r, s)
* 生成签名：` s = m/k + r/k*x`
* 验证签名：`sR = mG + rP`
  * 先求得R值：`R = m/s*G + r/s*P`
  * 再求得r值：`r = R.x` ，即R的x坐标值，验证r是否一致

## 随机值k选取的重要性

Schnorr与ECDSA均对随机值强依赖，若任意两次签名之间采用了相同的随机值k，则暴露出私钥。下面我们演算在随机值相同情况下，各个算法暴露私钥的过程。

### Schnorr暴露私钥过程

```
两次签名，随机值k相同，同一个把公钥P，两次签名消息分别为：m1, m2。
令 e1 = H(R||P||m1), e2 = H(R||P||m2)

s1 = k + H(R||P||m1)*x = k + e1*x
s2 = k + H(R||P||m2)*x = k + e2*x

有两个签名：(R, s1), (R, s2)

s1 - s2 = k + e1*x - (k + e2*x)
        = (e1 - e2)*x

那么私钥就可以得出：
x = (s1 - s2) / (e1 - e2)
```

### ECDSA暴露私钥过程

```
两次签名，随机值k相同，同一个把公钥P，两次签名消息分别为：m1, m2。

s1 = m1/k + r/k*x
s2 = m2/k + r/k*x

有两个签名：(r, s1), (r, s2)

第一步，将上述两个等式相减，先暴露出该随机值k，有：
k = (s1 - s2) / (m1 - m2)

根据k值，计算出其对应的r值：
r = R.x = (kG).x

第二步，反推出私钥：
x = (s1 - m1/k)*k/r 或
x = (s2 - m2/k)*k/r
```

根据上述过程，得出结论：相同私钥对任意数据签名，若任意两次签名采用相同的随机值，则立即暴露出私钥。

对于软硬件钱包来说，其随机数发生器将是非常重要的基石，绝不能出故障。

## 伪签名问题

### ECDSA伪签名

之所以说是伪签名，因为是可以验证通过的签名，并不是“无效签名”。CSW曾经多次使用此伎俩，制造中本聪公钥对应的伪签名，甚至骗过了一些不太清楚签名机制的人。

生成ECDSA的伪签名的过程：

```
ECDSA验证签名的等式为：
R = m/s*G + r/s*P

令 u=m/s, v=r/s
即 R = uG + vP

随机生成u、v的值，则有：
u'G + v'P = R'

得到 r' = R'.x，// 求X坐标
得到 s' = r'/v
得到 m' = u*s'

那么对于消息m'，其签名为：(r', s')。此签名是可以验证通过的，因为验证等式是平衡的。
```

防范ECDSA伪签名非常简单：不可由签名方提供消息m值，必须由验证方提供。因为在伪签名里，m值是随机生成的，一旦m值由其他人提供，则签名方是无法构造出平衡等式的。或者说伪签名里签名方是提供不了m值的消息原文的。

也就是说，由你写一句话下来，交给CSW去签名，他就无法提供出中本聪公钥下能够验证的签名了。

### Schnorr是伪签名免疫的

Schnorr目前是无法构造出伪签名的，因为无法构造出平衡的验证等式。在Schnorr的验证等式中`sG = R + H(R||P||m)P`：

* 若尝试随机等式左侧值（即随机s值），则无法找到合适的R'和m'，因为R值在哈希函数里使用了
* 若尝试随机等式右侧值（随机生成R和m值），`sG`是椭圆曲线乘法，曲线除法不可逆的情况下是无法找到对应的s'值

因此Schnorr签名算法是无法构造出能验证通过的伪签名的。

---

参考：

* https://github.com/sipa/bips/blob/bip-schnorr/bip-schnorr.mediawiki
* How Perfect Offline Wallets Can Still Leak Bitcoin Private Keys
  * https://bitcointalk.org/index.php?topic=883793.0
    * https://bitcointalk.org/index.php?topic=285142.msg3077694#msg3077694
    * https://www.mail-archive.com/bitcoin-development@lists.sourceforge.net/msg02721.html