title: Shadowsocks 加入比特币的支持
date: 2015-03-11 16:30:19
tags:
---

## 前言

加入比特币后达到的效果：只需知道服务提供方的比特币地址，就可以使用其服务。


假定`17j3frhsihr6bjtkbv4avlurxtc35b7j1e`是服务提供方的比特币地址，那么有如下约定：

* _17j3frhsihr6bjtkbv4avlurxtc35b7j1e_ 即为服务方的比特币收款地址
* _17j3frhsihr6bjtkbv4avlurxtc35b7j1e_ 即为共享密钥（配置参数password）
* _17j3frhsihr6bjtkbv4avlurxtc35b7j1e_**.com** 即为服务方的域名

假定客户端拥有地址 `1CntTWhxxxxxxxxxxxxxxxxxxxxxx` 及其私钥。首先保障该地址有一定数量的比特币，然后从该地址支付比特币至服务端地址，当服务方地址收到比特币后，将该客户地址添加到列表文件中，此时，客户即可使用对方的服务。

项目GitHub: [https://github.com/bitkevin/shadowsocks-libev](https://github.com/bitkevin/shadowsocks-libev)，如果shadowsocks接收的话会pull request回原来分支。

为了学习测试，可以使用测试服务器 `17j3frhsihr6bjtkbv4avlurxtc35b7j1e.com`，费用是每天 0.00015 btc。服务端每分钟更新一次支付记录。

## 编译

编译步骤没有变化，主要是配置文件的差异。

```
# build shadowsocks
apt-get install build-essential autoconf libtool libssl-dev
./configure && make
make install
```

## 服务端

写入付款地址的记录

```
mkdir -p /var/ss
echo "1CntTWhxxxxxxxxxxxxxxxxxxxxxx" >> /var/ss/list-ss-server.txt
```

`ss-server` 启动时，指定参数 `--bitcoin-list` 即可：

```
ss-server -s 0.0.0.0 -p 443 -k 17j3frhsihr6bjtkbv4avlurxtc35b7j1e -m aes-256-cfb -t 60 --workers 10 --fast-open --bitcoin-list /var/ss/list-ss-server.txt
```

同时，注册域名 `17j3frhsihr6bjtkbv4avlurxtc35b7j1e.com`，并将其A记录指向到提供服务的IP地址上。


## 客户端

示例配置文件：

```
{
    "server":"17j3frhsihr6bjtkbv4avlurxtc35b7j1e.com",
    "server_port":443,
    "local_port":8999,
    "password":"17j3frhsihr6bjtkbv4avlurxtc35b7j1e",
    "timeout":300,
    "method":"aes-256-cfb",
    "bitcoin_address":"1CntTWhxxxxxxxxxxxxxxxxxxxxxx",
    "bitcoin_privkey":"L5eixxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
}
```

* `bitcoin_address` 支付地址，推荐新生成一个地址单独用于此，并保证该地址是你个人控制的
* `bitcoin_privkey` 支付地址对应的私钥，base58格式，大部分钱包导出就是此格式。该私钥不会暴露至网络

保障地址 `1CntTWhxxxxxxxxxxxxxxxxxxxxxx` 有一定数量的比特币，然后从该地址付款至服务端地址 `17j3frhsihr6bjtkbv4avlurxtc35b7j1e`，付款比特币数量尽量以天为整数即可，例如付款30天的费用：0.0045 btc = 0.00015 * 30。

## 其他

为了方便，提供了一个自动生成付款地址记录的脚本。安装至目录 `/var/ss`：

```
apt-get install php5-cli php5-curl
mkdir -p /var/ss
cp scripts/generate.php /var/ss
```

拷贝后，需要修改其配置，generate.php 中的配置参数说明：

```
// 由于使用了 chain.com 的API，所以到 chain.com 注册一个账户，并免费获得一个API KEY。
$_CFG['API-KEY-ID'] = "DEMO-4a5e1e4";

// 服务端的地址，该地址用于收款等。
$_CFG['server_baddress'] = "17j3frhsihr6bjtkbv4avlurxtc35b7j1e";

// 每天的价格，单位是聪。 15000 Satoshi = 0.00015 Btc。
$_CFG['satoshi_per_day'] = 15000;
```

若 `ss-local`/`ss-server` 的配置文件中去掉 `--bitcoin-xxx` 参数，则与不带bitcoin支持的shadowsocks行为一致。
