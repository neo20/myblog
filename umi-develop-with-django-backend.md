---
title: Umi+Django开发环境搭建
date: 2020-03-25 15:15:00
tags: [开发,django,umi]
---
Umi是阿里推出的React脚手架，有路由、mock数据、数据绑定（dva）等功能。
Django是一个python的web开发框架，数据库管理很好用，开发中可以快速修改数据库。
本文介绍我如何在开发环境中使用这两个框架。

## 1. 创建umi项目
创建空目录
``` bash
mkdir 4example && cd 4example
```

建立目录结构
``` bash
yarn create @umijs/umi-app
```

安装依赖
``` bash
yarn
```

运行
``` bash
yarn start
```

输出如下：
```
yarn run v1.22.4
$ umi dev
Starting the development server...

✔ Webpack
  Compiled successfully in 15.10s

 DONE  Compiled successfully in 15108ms                                                           下午3:38:37


  App running at:
  - Local:   http://localhost:8000 (copied to clipboard)
  - Network: http://192.168.31.167:8000
```


## 2. 创建django项目

另创建一个终端，执行
``` bash
django-admin startproject mysite
```
进入mysite，执行
```
python manage.py runserver 7000
```
运行结果如下：
```
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).
March 25, 2020 - 07:44:51
Django version 3.0.4, using settings 'server.settings'
Starting development server at http://127.0.0.1:7000/
Quit the server with CONTROL-C.
```

## 3. 配置nginx转发

编辑nginx server块
```
server {

    listen       9000;
    server_name  localhost;
    
    location /api {
        proxy_pass http://127.0.0.1:7000;
        proxy_http_version 1.1;
    }    

    location / {
        proxy_pass http://127.0.0.1:8000/;
        proxy_http_version 1.1;
        # 下面两行为了转发 websocket
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "Upgrade";
    }
}
```

开发时访问`http://127.0.0.1:9000`就可以了

参考文献：
> 1. https://umijs.org/docs/getting-started#startup-project
> 2. https://docs.djangoproject.com/en/3.0/intro/tutorial01/