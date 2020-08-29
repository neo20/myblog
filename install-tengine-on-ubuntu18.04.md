---
title: 在Ubuntu18.04上安装Tengine
date: 2020-03-25 00:05:17
tags: [ubuntu18.04,nginx]
---
Tengine是淘宝维护的nginx分支。
在Ubuntu需要编译安装，比较顺利。

## 编译安装

1. 从官网下载安装包
 > http://tengine.taobao.org/download.html

2. 解压到任一目录
``` bash
tar -zxvf Tengine-2.3.2.tar.gz
```

3. 下载并解压依赖库到同目录
 > - pcre 注意不是pcre2
 > http://www.pcre.org/
 > - openssl
 > https://www.openssl.org/source/
 > - zlib
 > https://www.zlib.net/

4. 运行configure
``` bash
/configure --with-pcre=../pcre-8.44 --with-openssl=../openssl-1.1.1e --with-zlib=../zlib-1.2.11
```

5. make
``` bash
make
```

6. make install 
``` bash
sudo make install
```

## 设置路径并运行

安装完成，终端提示显示安装到`/usr/local/nginx`目录下
可以运行`echo $PATH`看一下可执行程序到目录，没有。我们加一个链接：
``` bash
sudo ln -s /usr/local/nginx/sbin/nginx  /usr/local/bin/nginx 
```
最后运行
``` bash
sudo nginx
```