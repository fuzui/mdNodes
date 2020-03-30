---
title: MongoDB用户 
tags: MongoDB
categories: MongoDB
date: 2019-04-17
---

#### 1. 启用认证 ####

在配置文件中添加
`vim /etc/mongod.conf`
```shell
security：
    authorization: enabled
```

#### 2. 添加用户 ####
（1）创建管理员账号
```shell
use admin
db.createUser{
	user:"root"
	pwd:"root"
	roles:[{role:"userAdminAnyDatabase",db:"admin"}]
}
```

（2）创建普通用户
```shell
use test
db.createUser{
	user:"wangze
	pwd:"123456"
	roles:[{role:"readWrite",db:"test"},
				{role:"read",db:"test1"}
				]
}
```
用户wangze存放在test数据库中，对test数据库有读写权限，对test1数据库只有读权限。

#### 3. 用户权限控制 ####
（1）查看用户权限
```shell
use test
db.getUser("wangze")
```

（2）授权
```shell
use test
db.grantRoleToUser(
	"wangze",
	[
		{role:"readWrite",db:"test2"}
	]
)
```
赋予wangze用户test2数据库的读写权限

（3）取消权限
```shell
use test
db.revokeRolesFromUser(
	"wangze",
	[
		{role:"readWrite",db:"test2"}
	]
)
```
收回wangze用户test2数据库的读写权限

#### 4. 用户登录 ####

两种方式
（1）mongo -uroot -proot --authenticationDatabase=admin
（2）mongo
	进入后使用`db.auth("root","root")`
	
#### 5. 修改密码 ####
`db.changeUserPassword("wangze","111111")`

#### 6. 删除用户 ####
`db.dropUser("wangze")`