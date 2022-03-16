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

​	第一部分：全局块

```shell
#user  nobody;
worker_processes  1; //nginx处理并发数量的值(值越大处理并发量越多)

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


```

​	第二部门：events块

```shell
events {
    worker_connections  1024; //nginx支持的最大连接数
}

```

​	第三部门：http块

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

