## Nginx

**反向代理** : 通过**反向代理服务器(Nginx)**将请求转发到不同的服务器获取数据,

​					**对外暴露的是代理服务器地址**，隐藏了真实的服务器地址。

**负载均衡** : **增加服务器的数量**，然后通过**反向代理服务器(Nginx)**将请求分发到不同的服务器，

​					将请求**平均分摊**到服务器集群，理想状态每台服务器请求数量相同

**动静分离** : **服务器(tomcat)部署动态资源**，**请求动态资源通过nginx**请求到tomcat服务器

​					请求静态资源，**nginx将请求指向静态资源服务器**



**nginx安装** : 

​	1.安装pcre依赖，将安装的压缩文件放到linux系统并解压，进入pcre目录执行./configure命令

​	2.使用make && make install，将文件编译并且安装

​	3.使用命令查看版本号

```shell
[root@centos7 pcre2-10.39]# pcre2-config --version
```
​	4.安装所需要的依赖包

```shell
[root@centos7 pcre2-10.39]# yum -y install make zilb zlib-devel gcc-c++ libtool openssl-devel
```

​	5.最后安装nginx

​	6.解压nginx压缩文件，执行命令

​	7.使用make && make install 完成安装

​	8.安装成功后在usr/local 多出nginx文件夹，在nginx/sbin里面有启动命令

```shell
[root@centos7 local]# ls
bin  etc  games  include  lib  lib64  libexec  nginx  sbin  share  src
[root@centos7 local]# cd nginx/
[root@centos7 nginx]# ls
conf  html  logs  sbin
[root@centos7 nginx]# cd sbin/
[root@centos7 sbin]# ls
nginx
[root@centos7 sbin]# 使用 ./nginx启动
```

**nginx操作命令(必须进入到nginx的/use/local/nginx/sbin目录)**

1.查看nginx版本号

```shell
[root@centos7 sbin]# ./nginx -v
nginx version: nginx/1.12.2
```

2.启动nginx服务

```shell
[root@centos7 sbin]# ./nginx 
nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Address already in use)
^[[Anginx: [emerg] bind() to 0.0.0.0:80 failed (98: Address already in use)
nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Address already in use)
nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Address already in use)
nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Address already in use)
nginx: [emerg] still could not bind()
[root@centos7 sbin]# ps -ef | grep nginx 
root      79404      1  0 20:29 ?        00:00:00 nginx: master process ./nginx
nobody    79405  79404  0 20:29 ?        00:00:00 nginx: worker process
root      79627  16662  0 20:29 pts/2    00:00:00 grep --color=auto nginx

```

3.关闭nginx

```shell
[root@centos7 sbin]# ./nginx -s stop
[root@centos7 sbin]# ps -ef | grep nginx 
root      78751  16662  0 20:28 pts/2    00:00:00 grep --color=auto nginx

```

4.重新加载nginx

```shell
[root@centos7 sbin]# ./nginx -s reload

```



**nginx配置文件**

​	**第一部分：全局块**

```shell
#user  nobody;
worker_processes  1; //nginx处理并发数量的值(值越大处理并发量越多)

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


```

​	**第二部门：events块**

```shell
events {
    worker_connections  1024; //nginx支持的最大连接数
}

```

​	**第三部门：http块**

```shell

http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server {
        listen       80;	//nginx监听的端口号
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html;
            index  index.html index.htm;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80

```

### 1.配置nginx的反向代理

（1）实现效果：打开浏览器，在地址栏输入www.123.com，跳转到linux系统的tomcat主页面

（2）准备

​		1）在linux系统安装tomcat，使用默认端口8080

​		2）把tomcat文件解压，进入tomcat/bin，输入./started

（3）nginx访问过程

​		对外暴露nginx的ip，通过nginx反向代理到tomcat

（4）在本地的host文件，配置域名映射的ip 192.168.159.128 www.123.com

（5）修改配置nginx配置文件

```shell
 server {
        listen       80;
        server_name  www.123.com; //映射地址

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html;
            proxy_pass http://127.0.0.1:8080;	//反向代理的地址
            index  index.html index.htm;
        }

        #error_page  404              /404.html;

```

### 2.配置nginx的反向代理实例2

​	1.访问 http://127.0.0.1:9001/edu/ 直接跳转到 127.0.0.1:8080

​	2.访问 http://127.0.0.1:9001/vod/ 直接跳转到 127.0.0.1:8081

​	（2）准备两个tomcat服务器 8080 | 8081

​	（3）创建文件夹和测试的页面

​	（4）配置nginx.conf文件

```shell
# another virtual host using mix of IP-, name-, and port-based configuration
    #
    server {
        listen       9001;
        server_name       192.168.159.128;
        location ~ /edu/ {
            proxy_pass http://127.0.0.1:8080
        }
		//反向代理到不同的端口号
        location ~/vod/ {
            proxy_pass http://127.0.0.1:8081
        }
    }
```

### Nginx配置负载均衡

实现效果：浏览器输入地址 http://192.168.159.128/edu/a.html，负载均衡效果，到8081和8080端口

（1）在tomcat 里面 webapps目录中，创建edu文件夹，在edu里面创建a.html

```shell

  upstream myserver {
  		ip_hash;
        server 192.168.159.128:8080 weight=5;
        server 192.168.159.128:8081 weight=10;
    }
    server {
        listen       80;
        server_name  192.168.159.128;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html;
            proxy_pass http://myserver;
            index  index.html index.htm;
        }

```

**nginx分配策略：**

轮询（默认），

权重（weight），

ip哈希（可以解决session共享问题），

fair（第三方）：根据后端响应时间分配



### Nginx配置动静分离

将动态请求（tomcat）和静态请求（静态资源服务器）分离，不仅仅是动态，静态资源的物理分离

（1）在linux系统准备静态资源，用于访问

（2）修改nginx配置文件

```shell
 server {
        listen       80;
        server_name  192.168.159.128;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location /www/ {
            root /usr/data/;
           # proxy_pass http://myserver;
            index  index.html index.html;
        }

        location /img/ {
            root /usr/data/;
            autoindex on;
        }

```

（3）浏览器输入地址 http://192.168.159.128/img/鸡.png; | http://192.168.159.128/www/test.html;



### Nginx配置高可用集群

（1）需要两台nginx服务器

（2）keepalived

（3）虚拟ip地址

**配置高可用准备**

（1）两台虚拟机

（2）两个nginx服务器

（3）安装keepalived

```shell
[root@centos7 usr]# yum install keepalived -y
已加载插件：fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
 //在/etc/下面生成keepalived文件

```

（4）修改keepalived的配置文件 

（5）完成主备配置

```java
! Configuration File for keepalived
global_defs {
   notification_email {
     acassen@firewall.loc
     failover@firewall.loc
     sysadmin@firewall.loc
   }
   notification_email_from Alexandre.Cassen@firewall.loc
   smtp_server 192.168.200.1
   smtp_connect_timeout 30
   router_id LVS_DEVEL	//访问到主机
   vrrp_skip_check_adv_addr
   vrrp_strict
   vrrp_garp_interval 0
   vrrp_gna_interval 0
}
    
vrrp_instance VI_1 {
    state MASTER	//主服务器master ,从服务器backup
    interface ens33  //绑定的网卡
    virtual_router_id 51 //主备的virtaul_router_id必须相同
    priority 100	//主备不同的优先级, 主机值较大
    advert_int 1	//每隔1s发送心跳检测是否存活
    authentication {
        auth_type PASS
        auth_pass 1111
    }
    //虚拟ip地址
     virtual_ipaddress {
        192.168.200.16
        192.168.200.17
        192.168.200.18
    }
}

// /etc/hosts文件
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
127.0.0.1 LVS_DEVEL
~                                                                                                                

//在usr/local/src/添加nginx检测脚本
#!/bin/bash
A=`ps -C nginx -no-header |wc -l`
if [ $A -eq 0 ]; then
    	/usr/local/nginx/sbin/nginx  #nginx启动位置
    	sleep 2
    	if [`ps -C nginx --no-header` |wc -l ` -eq 0 ];then
            killall keepalived
        fi
 fi

```

（6）把两台服务器的nginx和keepalived启动

```shell
		systemcl start keepalived.service
```

（7）访问虚拟ip

（8）停掉主服务器，访问虚拟ip也能正常访问，高可用

## Nginx原理

![img](https://images2017.cnblogs.com/blog/1183448/201802/1183448-20180210145226654-1347579045.png)

```java
[root@centos7 sbin]# ps -ef | grep nginx
root      42754      1  0 19:10 ?        00:00:00 nginx: master process ./nginx
nobody    42755  42754  0 19:10 ?        00:00:00 nginx: worker process
root      42808   3177  0 19:10 pts/1    00:00:00 grep --color=auto nginx
[root@centos7 sbin]# 
```

![img](https://pics7.baidu.com/feed/f9dcd100baa1cd11fb027fc104648ffbc2ce2d1c.jpeg?token=b4f851a3781a75cd41c5b23e9883c5b3)

worker**通过争抢机制**来进行反向代理等

1.一个master和多个worker的好处

​	（1）可以使用nginx -s reload **热部署方式**

​	（2）每个worker都是**独立的进程**，不需要加锁，保证服务不会中断

​	（4）需要设置多少个worker-------->**[数量和CPU数量相等]**

​			**Nginx同Redis类似都采用IO多路复用机制**

​	（5）一个请求，worker占用 2 个 或者 4个**连接数**

​	（6）worker支持的最大连接数 [1024]

​	（7）worker最大并发量	**[静态访问]**worker数 * 1024 / 2  || **[反向代理]** worker数 * 1024 / 4

​		

