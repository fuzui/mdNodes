---
title: centos7下搭建ngrok服务器进行内网穿透
date: 2019-11-07 16:08:52
tags: 内网穿透
categories: linux
---
<div align='center' ><font size='70'>centos7下搭建ngrok服务器进行内网穿透</font></div>

以下以阿里云centos7服务器为例，并且已经安装好git、golang

### 一、下载ngrok

```shell
cd /data/wwwroot
git clone https://github.com/tutumcloud/ngrok.git
```

### 二、生成ngrok使用的https证书

```shell
cd /data/wwwroot/ngrok
NGROK_DOMAIN="ngrok.test.com"
openssl genrsa -out base.key 2048
openssl req -new -x509 -nodes -key base.key -days 10000 -subj "/CN=$NGROK_DOMAIN" -out base.pem
openssl genrsa -out server.key 2048
openssl req -new -key server.key -subj "/CN=$NGROK_DOMAIN" -out server.csr
openssl x509 -req -in server.csr -CA base.pem -CAkey base.key -CAcreateserial -days 10000 -out server.crt
```
并替换
```shell
cp base.pem assets/client/tls/ngrokroot.crt
cp server.crt assets/server/tls/snakeoil.crt
cp server.key assets/server/tls/snakeoil.key
```

### 三、编译服务端
```shell
make release-server
```
生成`bin/ngrok`

### 四、编译客户端
```shell
GOOS=windows GOARCH=amd64 make release-client
```
生成`bin/windows_amd64/ngrok.exe`
把`ngrok.exe`下载到你电脑
>mac下编译： GOOS=darwin GOARCH=amd64 make release-client

### 五、运行服务端
后台运行
```shell
nohup ./bin/ngrokd -tlsKey=server.key -tlsCrt=server.crt -domain="ngrok.test.com" -httpAddr=":3080" -httpsAddr=":3043" >/dev/null 2>log &
```
>注意事项
    1、3080，3043端口可以改成80，443，如果这两个端口空闲的话。
    2、阿里云安全组开放3080、3043、4443端口，4443是默认的客户端通信端口。
    3、iptables开放3080、3043、4443端口。
    4、添加两条域名解析ngrok和*.ngrok，第二条记得带上*.，不然无法匹配xxx.ngrok.test.com三级域名。

### 六、运行客户端

编辑配置文件ngrok.cfg
```shell
server_addr: "ngrok.test.com:4443"
trust_host_root_certs: false
```
运行
```shell
ngrok -config=ngrok.cfg -subdomain abc 8080
```
### 七、如果80端口已经被其他程序占用，ngrok如何使用80端口，进行微信公众号开发本地调试？

因为微信公众号只支持80端口或443端口。
**方法一：**

如果你的服务器只用了80端口，那么你可以用443端口来开发。
**方法二：**

如果你的服务器80端口和443端口都被占用，且不能取消占用

1. 配置`ngrok.test.com`的80端口映射（使用nginx或apache对不同域名进行80端口映射）
2. 设置代理和反向代理/都指向`http://test.ngrok.test.com:9080/`
3. 客户端只要设置前缀为test的ngrok代理，就可以通过`ngrok.test.com/xxx`直接访问

为什么不直接指向`127.0.0.1:9080`?
试过了，没用，本地的9080端口虽然被`ngrok`占用，但无法直接通过`127.0.0.1:9080`访问。
所以我暂时只能再次发起http请求来进行转发。

**本文转自：**
*作者：王乐城愚人云端
链接：[https://www.jianshu.com/p/00794b151be7](https://www.jianshu.com/p/00794b151be7)*