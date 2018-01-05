---
title: Load Balancing with Nginx and Docker
layout: post
date: '2018-01-05 03:12:30'
tags: Docker
card-image: https://i.ytimg.com/vi/Njx9VAvUBWs/maxresdefault.jpg
post-card-type: image
---

这次利用Docker建立3个Containers， 分别是 Nginx，Django + uWSGI， Postgres (https://github.com/zengtian006/Load-Balancing-with-Nginx-and-Docker)

主要说有一下Nignx 里面的`Dockerfile`

```
FROM nginx:latest

//先將 nginx.conf 複製到 /etc/nginx/nginx.conf 的路徑
COPY nginx.conf /etc/nginx/nginx.conf

//將 my_nginx.conf 複製到 /etc/nginx/sites-available/ 裡面
COPY my_nginx.conf /etc/nginx/sites-available/

//將 sites-enabled 以及 my_nginx.conf 連結起來。
RUN mkdir -p /etc/nginx/sites-enabled/\
    && ln -s /etc/nginx/sites-available/my_nginx.conf /etc/nginx/sites-enabled/

CMD ["nginx", "-g", "daemon off;"]
```

`nginx.conf`

更改了两个地方
1, ``` user  root;  ```
2,
```
# include /etc/nginx/conf.d/*.conf;
include /etc/nginx/sites-available/*;
```
另外一个重要的文件 `my_nginx.conf`
```
# the upstream component nginx needs to connect to
upstream uwsgi {
    # server api:8001; # use TCP
     server demo_api_1:8001 weight=4; # use TCP
     server demo_api_2:8001 weight=6;  # use TCP
}

# configuration of the server
server {
    # the port your site will be served on
    listen    80;
    # index  index.html;
    # the domain name it will serve for
    # substitute your machine's IP address or FQDN
    server_name  tim.com www.tim.com;
    charset     utf-8;

    client_max_body_size 75M;   # adjust to taste

    # Django media
    # location /media  {
    #     alias /docker_api/static/media;  # your Django project's media files - amend as required
    # }

    location /static {
        alias /docker_api/static; # your Django project's static files - amend as required
    }

    location / {
        uwsgi_pass  uwsgi;
        include     /etc/nginx/uwsgi_params; # the uwsgi_params file you installed
    }

}
```
####Nginx 主要的 Load balance 有三种

***round-robin*** — requests to the application servers are distributed in a round-robin fashion

使用轮询的方式，也可以加上 weight 权重，让比较强的 Server 承受比较大的压力（ weight 设定高一点）。

***least-connected*** — next request is assigned to the server with the least number of active connections

此种方式，Nginx 会将请求给负载 Loading 较轻的 Server，避免负载（A Server） Loading 已经很大了，还将请求交给 A Server 处理。

***ip-hash*** — a hash-function is used to determine what server should be selected for the next request (based on the client's IP address)

不管是 round-robin 还是 least-connected 的 Load balance ，每个 User 送出的请求都可能会被分到不同的

Server，并不能保证同一个 User 每次的请求都一定会连到同一台 Server， 因为这个原因，於是有了 ip-hash，

透过这个方法，会使用 ip 的方式将同一个 User 的请求都导到同一台 Server （除非这台 Server 掛了 ）

介绍完了 Nginx 主要的三种 Load balance 演算法，至於要使用哪一种，就看你自己的需求了

更多范例以及详细的说明可参考官网
http://nginx.org/en/docs/http/load_balancing.html

`uwsgi.ini`
```
[uwsgi]

# http=0.0.0.0:8000
socket=:8001
master=true
# maximum number of worker processes
processes=4
threads=2
# Django's wsgi file
module=demo.wsgi:application

# chmod-socket=664
# uid=www-data
# gid=www-data

# clear environment on exit
vacuum          = true
```
设定完成后, 建立新的image  `docker-compose build`
执行启动Dokcer`docker-compose up -d --scale api=2`