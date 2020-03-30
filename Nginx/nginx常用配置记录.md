---
title: nginx常用配置记录
tags: Nginx
categories: Nginx
date: 2020-02-17
---

<div align='center' ><font size='70'>nginx常用配置记录</font></div>

# 一、前言

在平时用的nginx挺多，但是并不熟悉nginx的配置，所以打算查阅网上资料整理一篇笔记方便自己以后回顾。
![](https://oss.fuzui.net/oneblog/20200217191211320.png)
# 二、nginx目录结构

| 路径                                        | 类型     | 作用                                                                                                                                                    |
| ------------------------------------------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| /etc/logrotate.d/nginx                      | 配置文件 | Nginx日志轮转，用于logrotate服务的日志切割                                                                                                              |
| /etc/nginx                                  | 目录     | Nginx主配置文件夹                                                                                                                                       |
| /etc/nginx/nginx.conf                       | 配置文件 | Nginx主配置文件                                                                                                                                         |
| /etc/nginx/conf.d                           | 目录     |           Nginx主配置文件                                                                                                                                               |
| /etc/nginx/conf.d/default.conf              | 配置文件 | 默认配置样例                                                                                                                                            |
| /etc/nginx/fastcgi_params                   | 配置文件 | fastcgi映射关系，将 Nginx 中的变量翻译成 PHP 中能够理解的变量，详见附录1                                                                                |
| /etc/nginx/uwcgi_params                     | 配置文件 | fastcgi映射关系，将 Nginx 中的变量翻译成 Python 中能够理解的变量，详见附录1                                                                             |
| /etc/nginx/scgi_params                      | 配置文件 | fastcgi映射关系                                                                                                                                         |
| /etc/nginx/koi-utf                          | 配置文件 | 编码转换映射文件，用于在输出内容到客户端时，将一种编码转换到另一种编码。 koi8-r 到 utf-8                                                                |
| /etc/nginx/koi-win                          | 配置文件 | 编码转换映射文件，用于在输出内容到客户端时，将一种编码转换到另一种编码。 koi8-r 到 windows-1251                                                         |
| /etc/nginx/win-utf                          | 配置文件 | 编码转换映射文件，用于在输出内容到客户端时，将一种编码转换到另一种编码。 windows-1251 到 utf-8                                                          |
| /etc/nginx/mine.types                       | 配置文件 | 文件扩展名与文件类型映射表，nginx根据映射关系，设置http请求响应头的 Content-Type 值。当在映射表找不到时，使用 nginx.conf 中 default-type 指定的默认值。 |
| /usr/lib/systemd/system/nginx-debug.service | 配置文件 | 用于配置出系统守护进程管理器的管理方式                                                                                                                  |
| /usr/lib/systemd/system/nginx.service       | 配置文件 | 用于配置出系统守护进程管理器的管理方式                                                                                                                  |
| /etc/sysconfig/nginx                        | 配置文件 | 用于配置出系统守护进程管理器的管理方式                                                                                                                  |
| /etc/sysconfig/nginx-debug                  | 配置文件 | 用于配置出系统守护进程管理器的管理方式                                                                                                                  |
| /usr/lib64/nginx/modules                    | 目录     | Nginx模块目录                                                                                                                                           |
| /etc/nginx/modules                          | 目录     | Nginx模块目录                                                                                                                                           |
| /usr/sbin/nginx                             | 命令     | Nginx服务的启动管理的终端命令                                                                                                                           |
| /usr/sbin/nginx-debug                       | 命令     | Nginx服务的启动管理的终端命令                                                                                                                           |
| /usr/share/doc/nginx-1.12.2                 | 目录     | Nginx手册和帮助文件                                                                                                                                     |
| /usr/share/doc/nginx/COPYRIGHT              | 目录     | Nginx手册和帮助文件                                                                                                                                     |
| /usr/share/man/nginx.8.gz                   | 文件     | Nginx手册和帮助文件                                                                                                                                     |
| /var/cache/nginx                            | 目录     | Nginx的缓存目录                                                                                                                                         |
| /var/log/nginx                              | 目录     |      Nginx的日志目录                                                                                                                                                                                                               


# 三、配置文件 
## 1. 结构
```
# 全局块
...              
# events块
events {         
   ...
}
# http块
http      
{
    # http全局块
    ...   
    # 虚拟主机server块
    server        
    { 
        # server全局块
        ...       
        # location块
        location [PATTERN]   
        {
            ...
        }
        location [PATTERN] 
        {
            ...
        }
    }
    server
    {
      ...
    }
    # http全局块
    ...     
}
```
说明：以下配置并未很明确标明作用域，关于其作用范围可查阅官方文档。
### main全局块
>配置影响nginx全局的指令。
* `user nginx;`
	配置用户或者组
* `worker_processes auto;`
	允许生成的进程数，一般代表系统cpu核数一到两倍最好
* `worker_cpu_affinity 01 10;`
表示开启两个进程，第一个进程对应着第一个CPU内核，第二个进程对应着第二个CPU内核。
* `error_log /var/log/nginx/error.log;`
	制定日志路径，级别。这个设置可以放入全局块，http块，server块，级别以此为：`debug`|`info`|`notice`|`warn`|`error`|`crit`|`alert`|`emerg`
* `pid /run/nginx.pid;`
	设置pid存放路径,pid是控制系统中的一个重要的控制文件
* `include /usr/share/nginx/modules/*.conf;`
	加载动态模块配置
* `worker_rlimit_nofile 65535;`
	>更改worker进程的最大打开文件数限制。如果没设置的话，这个值为操作系统的限制。设置后你的操作系统和Nginx可以处理比“ulimit -a”更多的文件，所以把这个值设高，这样nginx就不会有“too many open files”问题了。
	>[nginx允许的最大连接数=（worker_connections）*（worker_processes）]<=worker_rlimit_nofile,另外worker_connections<ulimit -Sn小于系统允许的最大文件打开数）

### events块
>配置影响nginx服务器或与用户的网络连接。有每个进程的最大连接数，选取哪种事件驱动模型处理连接请求，是否允许同时接受多个网路连接，开启多个网络连接序列化等。
* `worker_connections 1024;`
	最大连接数。
* `accept_mutex on;`
	设置网路连接序列化，防止惊群现象发生，默认为on
* `multi_accept on;  `
	设置一个进程是否同时接受多个网络连接，默认为off
* `use epoll;`     
	设置用于复用客户端线程的轮询方法。如果你使用Linux 2.6+，你应该使用epoll。如果你使用FREEBSD，你应该使用kqueue。`select`|`poll`|`kqueue`|`epoll`|`resig`|`/dev/poll`|`eventport`
### http块
>可以嵌套多个server，配置代理，缓存，日志定义等绝大多数功能和第三方模块的配置。

* `log_format myFormat '$remote_addr–$remote_user [$time_local] $request $status $body_bytes_sent $http_referer $http_user_agent $http_x_forwarded_for'; `
	自定义日志格式            
  
* `access_log  /var/log/nginx/access.log  main;`    
	指令指定日志文件的存放路径和名称 eg`access_log [PATH] [日志名]`,取消改为`access_log off`。
  
* `sendfile            on;` 
	指定是否使用sendfile系统调用来传输文件。sendfile系统调用在两个文件描述符之间直接传递数据(完全在内核中操作)，从而避免了数据在内核缓冲区和用户缓冲区之间的拷贝，操作效率很高，被称之为零拷贝。
* `sendfile_max_chunk 1M;`
限制最大sendfile的文件大小，防止过大的文件占据整个工作进程
* `tcp_nopush          on;`     
	告诉nginx在一个数据包里发送所有头文件，而不一个接一个的发送。   tcp_nopush 必须和 sendfile 搭配使用。
	
* ` tcp_nodelay         on;`    
	为了解决Nagle和DelayedAcknowledgment的延迟问题。详情：[TCP_NODELAY 和 TCP_NOPUSH的解释](https://www.cnblogs.com/wajika/p/6573014.html)
* `keepalive_timeout   65;`      
	连接超时时间，这个设置可以放入http块、server块、location块
* `client_header_timeout 10;` 
	请求头超时时间
* `client_body_timeout 10; `
	请求体超时时间
* `reset_timedout_connection on;`  
	告诉nginx关闭不响应的客户端连接。这将会释放那个客户端所占有的内存空间。
*  `send_timeout 10;`  
	指定客户端的响应超时时间。这个设置不会用于整个转发器，而是在两次客户端读取操作之间。如果在这段时间内，客户端没有读取任何数据，nginx就会关闭连接。
* `limit_conn_zone $binary_remote_addr zone=addr:5m;`  
	设置用于保存各种key（比如当前连接数）的共享内存的参数。5m就是5兆字节，这个值应该被设置的足够大以存储`（32K*5）32byte状态或者（16K*5）64byte`状态。
*  `limit_conn addr 100;`  	
 	为给定的key设置最大连接数。这里key是addr，设置的值是100，也就是说允许每一个IP地址最多同时打开有100个连接。
* `types_hash_max_size 2048;`     
	影响散列表的冲突率。types_hash_max_size越大，就会消耗更多的内存，但散列key的冲突率会降低，检索速度就更快。types_hash_max_size越小，消耗的内存就越小，但散列key的冲突率可能上升。
* `gzip on;` 
	开启压缩，可作用于http，server， location块中，内置变量 $gzip_ratio 可以获取到gzip的压缩比率。**gzip压缩器需要在用户态进行，因此无法和sendfile共存，如果gizp设置为on，那么sendfile就会失去作用**。
* `gzip_types text/html text/plain text/css;`
 表示压缩的文件类型，还可以添加web字体格式、ioc图标和SVG图像等其他类型文件。
* `gzip_min_length 512; `  
	告诉Nginx不要压缩小于512字节的文件。这是非常小的文件，可以不用压缩      
* `gzip_comp_level 2;`
	压缩级别，越高压缩比越大，越消耗cpu
* `gzip_http_version 1.0`
	gzip支持http协议
* `gzip_proxied any;`
	Nginx作为反向代理的时候启用，开启或者关闭后端服务器返回的结果.
	>参数：
	>off - 关闭所有的代理结果数据的压缩
expired - 启用压缩，如果header头中包含 "Expires" 头信息
no-cache - 启用压缩，如果header头中包含 "Cache-Control:no-cache" 头信息
no-store - 启用压缩，如果header头中包含 "Cache-Control:no-store" 头信息
private - 启用压缩，如果header头中包含 "Cache-Control:private" 头信息
no_last_modified - 启用压缩,如果header头中不包含 "Last-Modified" 头信息
no_etag - 启用压缩 ,如果header头中不包含 "ETag" 头信息
auth - 启用压缩 , 如果header头中包含 "Authorization" 头信息
any - 无条件启用压缩
* `gzip_buffers 16 8k;`
	设置用于处理请求压缩的缓冲区数量和大小。
* `gzip_vary on;`
	增加响应头”Vary: Accept-Encoding”，首先判断客户端是否支持gzip压缩技术,不支持的不就不进行gzip技术压缩
* `gzip_disable "MSIE [1-6].(?!.*SV1)"`
	表明哪些UA头不使用gzip压缩
* `include             /etc/nginx/mime.types;`         
	文件扩展名与文件类型映射表                           
* `default_type        application/octet-stream;`     
	默认文件类型
* `server_tokens off;`      
	隐藏版本号，加强安全性。 
* `open_file_cache  max=100000 inactive=20s;` 
	打开缓存的同时也指定了缓存最大数目，以及缓存的时间。可以设置一个相对高的最大时间，这样可以在它们不活动超过20秒后清除掉。
* `open_file_cache_valid 30s;` 
 在open_file_cache中指定检测正确信息的间隔时间。
* `open_file_cache_min_uses 2`
 定义了open_file_cache中指令参数不活动时间期间里最小的文件数。
* `open_file_cache_errors on`
 指定了当搜索一个文件时是否缓存错误信息，也包括再次给配置中添加文件。我们也包括了服务器模块，这些是在不同文件中定义的。如果你的服务器模块不在这些位置，你就得修改这一行来指定正确的位置。                                            
* `include /etc/nginx/conf.d/*.conf; ` 
	引入子配置文件
### server块
>配置虚拟主机的相关参数，一个http中可以有多个server。
* `keepalive_requests 120;` 
	单连接请求上限次数。
* `listen       80;`
	监听端口
* `server_name  ztool.cloud;`   
	监听地址(ip或域名)
### location块
* `root /data/java;` 
	根目录。
* `index  /data/java/index.html;`
	默认首页
* `proxy_pass http://127.0.0.1:8080;`   
	代理转发
*  `proxy_connect_timeout 15s;`
	代理连接超时时间
*  `proxy_send_timeout 15s;`
	服务器数据发送超时时间
*  `proxy_read_timeout 15s;`
	数据读取超时时间
*   `proxy_http_version 1.1;`
	http协议
* `proxy_buffer_size  4k;`
	设置代理服务器（nginx）从后端服务器读取并保存用户头信息的缓冲区大小。它仅用于限定 headers 的 buffer 区，所以它的值比 proxy_buffers 更低。
* `proxy_buffers 4  32k;`   
	指定请求缓存的数量和大小
*  `proxy_busy_buffers_size 64k;`
	高负荷下缓冲大小
*  `proxy_temp_file_write_size 64k;`
	指定proxy缓存临时文件的大小
*  `proxy_temp_path /usr/local/nginx/proxy_temp 1 2;`
	当缓存被代理的服务器响应到临时文件时，这个选项限制每次写临时文件的大小。
*   `proxy_set_header  X-Real-IP $remote_addr; `
 	 `proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for; `
	设置由后端服务器获取用户主机或真实IP地址，以及代理者的真实IP。
* `add_header X-Frame-Options SANEORIGIN;`
	proxy_set_header是Nginx设置请求头信息给上游服务器，add_header是Nginx设置响应头信息给浏览器。

··

# 四、附录
附录1
```
fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;#脚本文件请求的路径,也就是说当访问127.0.0.1/index.php的时候，需要读取网站根目录下面的index.php文件，如果没有配置这一配置项时，nginx不回去网站根目录下访问.php文件，所以返回空白
fastcgi_param QUERY_STRING $query_string;                        #请求的参数;如?app=123
fastcgi_param REQUEST_METHOD $request_method;                    #请求的动作(GET,POST)
fastcgi_param CONTENT_TYPE $content_type;                        #请求头中的Content-Type字段
fastcgi_param CONTENT_LENGTH $content_length;                    #请求头中的Content-length字段。

fastcgi_param SCRIPT_NAME $fastcgi_script_name;                  #脚本名称 
fastcgi_param REQUEST_URI $request_uri;                          #请求的地址不带参数
fastcgi_param DOCUMENT_URI $document_uri;                        #与$uri相同。 
fastcgi_param DOCUMENT_ROOT $document_root;                      #网站的根目录。在server配置中root指令中指定的值 
fastcgi_param SERVER_PROTOCOL $server_protocol;                  #请求使用的协议，通常是HTTP/1.0或HTTP/1.1。

fastcgi_param GATEWAY_INTERFACE CGI/1.1;                         #cgi 版本
fastcgi_param SERVER_SOFTWARE nginx/$nginx_version;              #nginx 版本号，可修改、隐藏

fastcgi_param REMOTE_ADDR $remote_addr;                          #客户端IP
fastcgi_param REMOTE_PORT $remote_port;                          #客户端端口
fastcgi_param SERVER_ADDR $server_addr;                          #服务器IP地址
fastcgi_param SERVER_PORT $server_port;                          #服务器端口
fastcgi_param SERVER_NAME $server_name;                          #服务器名，域名在server配置中指定的server_name

fastcgi_param PATH_INFO $path_info;                             #可自定义变量

-- PHP only, required if PHP was built with --enable-force-cgi-redirect
fastcgi_param REDIRECT_STATUS 200;
```


# 五、参考
[Nginx服务学习(4)-目录说明](https://blog.51cto.com/11512826/2056765)
[Nginx 配置详解](https://www.runoob.com/w3cnote/nginx-setup-intro.html)
[Nginx配置性能优化的方法](https://blog.51cto.com/jschu/1760366)
[Nginx的配置文件详解（超详细）](https://blog.csdn.net/wangbin_0729/article/details/82109693)
