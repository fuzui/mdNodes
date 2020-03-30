---
title: websocket操作bitshares
tags: 区块链
categories: 区块链
date: 2019-08-21
---

#### 一、安装 ####

```shell
npm install wscat
```
#### 二、连接 ####
```shell
wscat -c wss://internal.api.magicw.net/ws
```

#### 三、操作 ####
1、登录到完整节点

```shell
{"id":0, "method":"call", "params":[1, "login", ["",""]]}
```
2、获取指定标识的API详情
```shell
{"id":0, "method":"call", "params":[1, "database", []]}
```
3、获取账号详情
```shell
{"id":1, "method":"call", "params":[0,"get_accounts",[["1.2.1653237"]]]}
```
4、根据id查询账号余额
```shell
{"id":0, "method":"call", "params":[0, "get_account_balances", ["1.2.1653237", []]]}
```

5、根据账户名查询账号id
```shell
{"id":0, "method":"call", "params":[0, "get_account_by_name", ["fuzui999", []]]}
```

6、得到账户总数
```shell
{"id":0, "method":"call", "params":[0, "get_account_count", [""]]}
```
7、获取资产信息
```shell
{"id":0, "method":"call", "params":[0, "lookup_asset_symbols", [["1.3.0"]]]}
```
8、得到chain-id
```shell
chain_id"
```

9、获取账户历史
```shell
{"id":0, "method":"call", "params":["history", "get_account_history", ["1.2.1653239","1.11.10","100","1.11.0"]]}
```

10、根据区块号位置获取某一条详细记录（块高等）
```shell
{"id":0, "method":"call", "params":[0, "get_transaction", [40101399,8]]}
```


