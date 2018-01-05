---
title: Deploy Django Application with Docker
layout: post
date: '2018-01-05 03:03:45'
tags: Docker
card-image: https://pbs.twimg.com/profile_images/862037907862765568/pYgBswUk_400x400.jpg
post-card-type: image
---


我们目前已经建立好了一个DRF 的 [Application](https://github.com/zengtian006/Django-REST-framework)

我们在 `Setting.py` 里面做一些调整，将之前的SQLite 数据库 改成 PostgreSQL
```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql_psycopg2',
        'NAME': 'postgres',
        'USER': 'postgres',
        'PASSWORD': 'password123',
        'HOST': 'db',
        'PORT': 5432,
    }
}
```

接下来是 Dockerfile 和 docker-compose.yml

`Dockerfile`
```
FROM python:3.6.2  //拉 python 的image下来
ENV PYTHONUNBUFFERED 1 //Avoid getting unwanted buffering
RUN mkdir /docker_api //创建工作目录
WORKDIR /docker_api  //指定工作目录
COPY . /docker_api/   //将所有files放到工作目录下
RUN pip install -r requirements.txt //执行安装 看下面
```
`requirements.txt`
```
 django<2.0
psycopg2
djangorestframework
```
更多reference  https://docs.docker.com/engine/reference/builder/

接下来看docker-compose
`docker-compose.yml`
```
version: '3'
services:

    db:
      container_name: 'postgres'  custom container 名称
      image: postgres
      environment:
        POSTGRES_PASSWORD: password123
      ports:
        - "5432:5432"
        # (HOST:CONTAINER)
      volumes:
        - pgdata:/var/lib/postgresql/data/

    web:
      build: ./api
      command: python manage.py runserver 0.0.0.0:8000 //启动Django server
      restart: always
      volumes:
        - api_data:/docker_api
        # (HOST:CONTAINER)
      ports:
        - "8000:8000"
        # (HOST:CONTAINER)
      depends_on:  //express dependency
        - db

volumes: //create volumes
    api_data:
    pgdata:
```
更多reference  https://docs.docker.com/compose/compose-file/#compose-file-structure-and-examples

设定完成后 执行启动Dokcer`docker-compose up`

###推荐一个Docker GUI管理工具
功能强大的 [Portaniner](https://github.com/portainer/portainer)
安装方法可参考 https://portainer.io/install.html
简单来说就是
```
docker volume create portainer_data
docker run --name=portainer -d -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer
```
之后在浏览器运行 http://localhost:9000/	进入管理界面
