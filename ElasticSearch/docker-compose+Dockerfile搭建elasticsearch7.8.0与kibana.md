---
title: docker-compose+Dockerfile搭建elasticsearch7.8.0与kibana
tags: [elasticsearch,kibana]
categories: elasticsearch
date: 2020-12-03
---

# 一、环境准备

* centos7服务器

* docker

  使用最新的docker，可通过`docker version`查看本地版本。

  docker升级：

  `rpm -qa | grep docker`列出当前信息

  ```
  docker-client******
  docker-common******
  ```

  使用`yum remove`卸载以上全部

  ```shell
  yum remove docker-client******
  ```

  使用`curl`升级（可能有些慢）

  ```shell
  curl -fsSL https://get.docker.com/ | sh
  ```

  重启`docker`,设置开启自启

  ```shell
  systemctl restart docker
  ```

  ```shell
  systemctl enable docker
  ```

  使用`docker version`查看版本信息。

  使用`docker images`会发现之前镜像还在。

  如启动旧镜像时报错:`Unknown runtime specified docker-runc`

  原因为从不兼容的版本升级docker ，解决方法:

  ```shell
  grep -rl 'docker-runc' /var/lib/docker/containers/ | xargs sed -i 's/docker-runc/runc/g'
  ```

  docker镜像加速:

  在国内服务器不使用镜像加速的话可能会很慢。

  我使用的阿里云的镜像加速，前往[阿里云容器镜像服务](https://cr.console.aliyun.com/)，登录后点击左侧菜单栏最后一项`镜像加速器`，会显示镜像地址以及配置方法

  ![](https://oss.fuzui.net/img/20201203014352.png)

* docker-compose

  因官方下载过慢，这里使用[DaoCloud](https://get.daocloud.io/#install-compose)

  ```shell
  curl -L https://get.daocloud.io/docker/compose/releases/download/1.27.4/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
  chmod +x /usr/local/bin/docker-compose
  ```

  通过`docker-compose version`查看版本信息

* 修改`vm.max_map_count`

  ```shell
  vim /etc/sysctl.conf
  ```

  最后添加`vm.max_map_count=655360`

  重新载入

  ```shell
  sysctl -p
  ```

* 检测IP转发功能是否开启

  输入`sysctl net.ipv4.ip_forward`,

  若返回`“net.ipv4.ip_forward = 0”`，则需要做以下修改：

  输入`vim /etc/sysctl.conf`编辑`sysctl.conf`，添加或修改`net.ipv4.ip_forward=1`

# 二、编写配置

> 以下我采用自己的数据盘作为存放与运行路径，可使用其他路径/data/

## 1.创建`elasticsearch`数据文件夹与配置文件

创建数据及日志文件

```shell
mkdir -p /data/elasticsearch/data
mkdir -p /data/elasticsearch/logs
mkdir -p /data/elasticsearch/config
mkdir -p /data/elasticsearch/plugins
mkdir -p /data/kibana/config
```

编写`elasticsearch`配置文件

```shell
vim /data/elasticsearch/config/elasticsearch.yml
```

```yaml
#cluster.name: my-application
node.name: node-1
#node.attr.rack: r1
path.data: /usr/share/elasticsearch/data
path.logs: /usr/share/elasticsearch/logs
#bootstrap.memory_lock: true
network.host: 0.0.0.0
http.port: 9200
#discovery.seed_hosts: ["host1", "host2"]
cluster.initial_master_nodes: ["node-1"]
#gateway.recover_after_nodes: 3
#action.destructive_requires_name: true
http.cors.enabled: true
http.cors.allow-origin: "*"
http.cors.allow-headers: Authorization
xpack.security.enabled: true
xpack.security.transport.ssl.enabled: true
xpack.security.audit.enabled: true
xpack.license.self_generated.type: basic
xpack.security.transport.ssl.keystore.type: PKCS12
xpack.security.transport.ssl.verification_mode: certificate
xpack.security.transport.ssl.keystore.path: elastic-certificates.p12
xpack.security.transport.ssl.truststore.path: elastic-certificates.p12
xpack.security.transport.ssl.truststore.type: PKCS12
```



## 2.创建`kibana`配置文件

```shell
vim /data/kibana/config/kibana.yml
```

```yaml
server.port: 5601
server.host: "0.0.0.0"
elasticsearch.hosts: ["http://elasticsearch:9200"]
elasticsearch.username: "elastic"
elasticsearch.password: "w12345"
i18n.locale: "zh-CN"
```



## 3.创建`docker-compose.yml`和`Dockerfile`

```shell
mkdir -p /data/dockers/elasticsearch
```

```shell
vim /data/docckers/elasticsearch/docker-compose.yml
```

```yaml
version: '3'
services:
  elasticsearch:
    image: elasticsearch
    build:
        context: .
        dockerfile: ES-Dockerfile
    container_name: elasticsearch
    restart: always
    volumes:
      - /data/elasticsearch/data:/usr/share/elasticsearch/data:rw
      - /data/elasticsearch/logs:/usr/share/elasticsearch/logs:rw
      - /data/elasticsearch/config/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml:rw
      - /data/elasticsearch/plugins:/usr/share/elasticsearch/plugins:rw
    environment:
      - "ES_JAVA_OPTS=-Xms1024m -Xmx1024m"
    hostname: elasticsearch
    ulimits:
      memlock:
        soft: -1
        hard: -1
    ports:
      - "9201:9200"
    networks:
      - esnet
  kibana:
    image: docker.io/kibana:7.8.0
    container_name: kibana
    ports:
      - 5602:5601
    volumes:
      - /data/kibana/config/kibana.yml:/usr/share/kibana/config/kibana.yml:rw
    networks:
      - esnet
      
networks:
  esnet:
```

```shell
vim /data/docckers/elasticsearch/ES-Dockerfile
```

```dockerfile

FROM elasticsearch:7.8.0
USER elasticsearch

#生成证书，密码可自己配置,与kibana.yml中一致
RUN bin/elasticsearch-certutil ca --out config/elastic-stack-ca.p12 --pass w12345

#生成证书，密码可自己配置,与kibana.yml中一致
RUN bin/elasticsearch-certutil cert --ca config/elastic-stack-ca.p12 --ca-pass w12345 --out config/elastic-certificates.p12 --pass w12345

#创建keystore
RUN bin/elasticsearch-keystore create

#将密码添加至keystore
RUN sh -c '/bin/echo -e "w12345" | sh bin/elasticsearch-keystore add xpack.security.transport.ssl.keystore.secure_password'
RUN sh -c '/bin/echo -e "w12345" | sh bin/elasticsearch-keystore add xpack.security.transport.ssl.truststore.secure_password'

#文件赋权限
RUN chmod 777 /usr/share/elasticsearch/config/elastic-certificates.p12
RUN chmod 777 /usr/share/elasticsearch/config/elastic-stack-ca.p12
```



# 三、创建用户及授权

* 添加启动用户

  添加用户elasticsearch

  ```shell
  adduser elasticsearch
  ```

  创建用户密码

  ```shell
  passwd elasticsearch
  ```

  授权

  ```shell
  cd /data/
  chown -R elasticsearch elasticsearch
  cd dockers/
  chown -R elasticsearch elasticsearch
  ```

* 将elasticsearch用户添加至docker用户组

  创建docker用户组

  ```shell
  groupadd docker
  ```

  加入docker用户组

  ```shell
  usermod -G docker elasticsearch
  ```

  重启docker服务

  ```shell
  systemctl restart docker
  ```

  

# 四、启动

1. 切换用户

   ```shell
   su elasticsearch
   ```

2. 构建

   ```shell
   cd /data/dockers/elasticsearch/
   ```

   ```shell
   docker-compose build
   ```

   此处如果不使用镜像加速，那么拉取kibana时可能会非常慢。

3. 启动

   应先前台启动，查询是否有异常、报错

   ```shell
   docker-compose up
   ```

   再开一个窗口进入elasticsearch docker设置密码：

   执行

   ```
   ./bin/elasticsearch-setup-passwords interactive --verbose
   ```

   按提示信息输入密码`w12345`

   > 此处密码需与`kibana.yml`中相同。

   设置完成后回到上个窗口`ctrl+c`停止。

   再次启动查看是否成功:

   ```shell
   docker-compose up
   ```

   

   如果出现以下图中信息，则启动成功

   ![](https://oss.fuzui.net/img/20201203140003.png)

   因为上面配置时，宿主机使用5602对应容器的5601端口，所以将安全组放开、防火墙放开端口后(或nginx转发后)，可在自己电脑浏览器中访问:`服务器ip:5602`

   ![](https://oss.fuzui.net/img/20201203140308.png)

   输入上方配置的用户名密码：`elastic`和`w12345`

   ![](https://oss.fuzui.net/img/20201203140456.png)

4. 后台启动

   启动无误后`ctrl+c`结束，使用`docker-compose up -d`后台启动

   ![](https://oss.fuzui.net/img/20201203141827.png)



# 五、扩展

## 1.设置elasticsearch运行内存

上面配置的是1G内存。在`docker-compose.yml`中elasticsearch的环境:

即第15行

```
- "ES_JAVA_OPTS=-Xms1024m -Xmx1024m"
```

修改完成后依次执行以下命令:先停止现有docker—>销毁—>构建—>启动

```shell
cd /data/dockers/elasticsearch
docker-compose stop
docker-compose down
docker-compose build
docker-compose up -d
```

## 2.IK 分词器

* 进入扩展文件夹

  ```shell
  cd /data/elasticsearch/plugins
  ```

* 下载压缩包

  ```shell
  wget https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v7.8.0/elasticsearch-analysis-ik-7.8.0.zip
  ```

  

* 解压

  > 需使用`unzip`,输入`unzip -v`检测是否存在，若没有则安装`yum install -y unzip zip`

  * 创建ik文件夹

    ```shell
    mkdir ik/
    ```

  * 解压至`ik`文件夹

    ```shell
    unzip -d ik/ elasticsearch-analysis-ik-7.8.0.zip
    ```

  * 删除压缩包

    ```shell
    rm -rf elasticsearch-analysis-ik-7.8.0.zip 
    ```

* 重启docker

  ```shell
  cd /data/dockers/elasticsearch
  ```

  ```shell
  docker-compose restart
  ```

> 以后修改其配置后，例如自定义dic等，都需要重启elasticsearch docker，可以通过docker或docker-compose来重启。

* 查看加载情况

  ```shell
  docker-compose logs -f elasticsearch
  ```

  ![](https://oss.fuzui.net/img/20201203183601.png)



# 六、说明

> 本次只搭建了一个节点，如需搭建多个节点的，可查看下方参考链接中。



**参考:**

[如何将Docker升级到最新版本](https://blog.csdn.net/qq_39629343/article/details/80168084)

[升级Docker后重启容器出现错误](http://www.tbaike.com/?/article/48)

[docker-compose + elasticsearch7.6(配置密码及证书) + kibana7.6 + elasticsearch-head搭建集群](https://blog.csdn.net/qq_20280007/article/details/108358156)

