---
title: PowerDesigner连接MySQL和逆向工程图
date: 2019-05-12 23:21:27
tags: powerdesigner
categories: 工具
---
<div align='center' ><font size='70'>PowerDesigner连接MySQL和逆向工程图</font></div>

### 0、写在前面的话

最近想梳理公司项目的表间关系，从项目后台管理系统的操作入手，以及代码的hibernate注解入手，都不算特别尽人意，于是最后还是鼓捣了一下PowerDesigner的逆向工程图，这样更直观一些。

想着以后不论项目切换或者接手的时候肯定是用得上的，所以在这里也记录一下，毕竟，好记性不如烂笔头，更何况我这还不是好记性。看网上有个哥们说他已经是三次忘了步骤了，所以我吸取教训赶紧第一次就记录下来。

### 1、MySQL数据库连接（JDBC方式）
JDBC的配置方式需要一些基础的环境和准备，但是也很简单，无非也就是JDK和mysql的连接jar包，这里不再展开阐述。
#### 1.1 新建一个pdm，dbms选择mysql
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/1007017-20180612095640002-283316605.png)
#### 1.2 Database - Connect  选择数据库连接
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/1007017-20180612095640508-571441195.png)![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/1007017-20180612095640508-571441195.png)
#### 1.3 配置连接信息
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/1007017-20180612095640959-1250308803.png)
数据库连接这里是通过一个配置文件来获取连接信息的，首次的话因为没有，所以我们需要选择Configure进行配置。

#### 1.4 填写配置信息

![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/1007017-20180612095641414-961686070.png)
如图，选择添加数据库资源，出现如上，相关说明如下：

*    Connection profile name：JDBC配置文件名称，可随意填写
 *   Directory：配置文件保存路径
 *   Description：配置文件描述，可根据实际用途填写
 *   Connection type：连接方式，这里我们选择JDBC
 *   DBMS type：数据库类型，提供大部分主流数据库选择，我们选择MySQL
 *   User name：登录数据库的用户名
 *   JDBC driver class：指定驱动类，使用默认的com.mysql.jdbc.Driver
 *   JDBC connection URL：连接URL，格式jdbc:mysql://ServerIP/Hostname:port/database
 *   JDBC driver jar files：指定连接的jar包路径
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/1007017-20180612095641803-1261491304.png)

#### 1.5 连接测试和配置保存
如图填写信息完成后，点击左下角的 Test Connection，出现成功提示则说明连接可行：
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/1007017-20180612095642254-1736432433.png)
如果测试连接不通过，且出现 Non SQL Error : Could not load class com.mysql.jdbc.Drive 的错误，而指定的jar包没有问题，那么是因为PowerDesigner无法找到驱动所产生的。解决办法是配置系统的classpath路径，指定jar包路径就好了。

![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/1007017-20180612095642649-1458279617.png)成功连接后，我们一路确定下去把这个配置文件进行保存，最终你可以在你指定的文件夹（该目录没有限制，自定义一个目录即可，此处我是建立在安装文件下的一个userConf文件夹内）中看到这个保存好的文件：
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/1007017-20180612095643062-1075647829.png)
### 2、从已有数据库中的表进行逆向工程图
#### 2.1 菜单选择，从数据库更新模型
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/1007017-20180612095643505-1924976606.png)
#### 2.2 选择数据库连接配置文件 
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/1007017-20180612095643846-1458854354.png)
#### 2.3 选择涉及的数据库和想要导出的表
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/1007017-20180612095644201-86187701.png)
#### 2.4 大功告成
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/1007017-20180612095644663-1890518121.png)
### 3、参考链接：
* [PowerDesigner连接MySQL数据库](https://www.jianshu.com/p/015b0eddd6ed)
* [powerdesigner16.5逆向工程，从远程oracle11g数据库导出PDM](https://blog.csdn.net/dragonpeng2008/article/details/52181207)
* [https://uule.iteye.com/blog/895150](https://uule.iteye.com/blog/895150)
<br>
<font color="blue" size="6">
本文转自：[PowerDesigner连接MySQL和逆向工程图](https://www.cnblogs.com/deng-cc/p/6824946.html#4253885)
</font>
<br>
* 如果连接时出现错误：
 <font color="red">SQLSTATE = 01S00
The server time zone value ‘ÖÐ¹ú±ê×¼Ê±¼ä’ is unrecognized or represents more than one time zone. You must configure either the server or JDBC driver (via the serverTimezone configuration property) to use a more specifc time zone value if you want to utilize time zone support.
</font>
因为时区问题
在连接上url上加`serverTimezone=GMT%2B8`
例如：`jdbc:mysql://localhost/数据库名?serverTimezone=GMT%2B8`

* 最后提供powerdesigner16.6.5版本下载（带激活方法，替换文件即可）
百度网盘：[点我下载](https://pan.baidu.com/s/1UA4ESV0QRG491Bdd0V2VcA)
提取码：wgjt



