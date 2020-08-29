---
title: 在Ubuntu18.04上使用nginx搭建php网站
date: 2020-03-25 00:05:17
tags: [ubuntu18.04,php,nginx]
---
## 配置php-fpm

首先安装`php-fpm`
``` bash
sudo apt install php-fpm
```

安装完成后，没有提示任何信息。尝试运行
``` bash
sudo service php7.2-fpm start
```

运行`sudo netstat -tlnp`查看，没有监听任何端口。那么socket文件在哪里？

使用`systemctl status`查看`php-fpm`状态
``` bash
systemctl status php7.2-fpm
```

可以看到
```
● php7.2-fpm.service - The PHP 7.2 FastCGI Process Manager
   Loaded: loaded (/lib/systemd/system/php7.2-fpm.service; enabled; vendor preset: enabled)
   Active: active (running) since Tue 2020-03-24 23:41:06 CST; 26min ago
     Docs: man:php-fpm7.2(8)
 Main PID: 14696 (php-fpm7.2)
   Status: "Processes active: 0, idle: 2, Requests: 38, slow: 0, Traffic: 0req/sec"
    Tasks: 3 (limit: 1106)
   CGroup: /system.slice/php7.2-fpm.service
           ├─14696 php-fpm: master process (/etc/php/7.2/fpm/php-fpm.conf)
           ├─14717 php-fpm: pool www
           └─14718 php-fpm: pool www
```

从中找出配置文件`/etc/php/7.2/fpm/php-fpm.conf`
搜素sock无果，查看后可以看到这样一行：
```
pid = /run/php/php7.2-fpm.pid
```

socket文件一般和它同目录，查看果然是。
``` bash
ls -al /run/php/
```

此外，如果php少某些库，可以这样搜索
``` bash
sudo apt search php7.2-[extname]
```

## 配置nginx

然后配置nginx，像这样就可以了

```
server {
    listen       80;
    server_name  neo20.cn;

    index   index.php;
    root /var/www/wordpress;

    location ~ \.php$ {
        include fastcgi.conf;
        fastcgi_pass unix:///run/php/php7.2-fpm.sock;
    }

}
```

最后重启nginx
``` bash
sudo nginx -s reload
```

额外说一下：注意`include fastcgi.conf;`这行。`fastcgi.conf`是相对路径，这个文件在哪？

我们可以查看运行`nginx -m`，输出为：
```
...
nginx:     ngx_http_not_modified_filter_module (static)
...
nginx: the configuration file /usr/local/nginx/conf/nginx.conf syntax is ok
...
```
`fastcgi.conf`这个文件就在配置文件所在的目录，名称可能不太相同。
运行
``` bash
ls -al /usr/local/nginx/conf/
```
输出
```
-rw-r--r-- 1 root root 1077 Mar 24 02:20 fastcgi.conf
-rw-r--r-- 1 root root 1077 Mar 24 02:24 fastcgi.conf.default
-rw-r--r-- 1 root root 1007 Mar 24 02:20 fastcgi_params
-rw-r--r-- 1 root root 1007 Mar 24 02:24 fastcgi_params.default
-rw-r--r-- 1 root root 2837 Mar 24 02:24 koi-utf
-rw-r--r-- 1 root root 2223 Mar 24 02:24 koi-win
-rw-r--r-- 1 root root 5231 Mar 24 02:20 mime.types
-rw-r--r-- 1 root root 5231 Mar 24 02:24 mime.types.default
-rw-r--r-- 1 root root 1247 Mar 24 23:29 nginx.conf
-rw-r--r-- 1 root root 3509 Mar 24 02:24 nginx.conf.default
-rw-r--r-- 1 root root  636 Mar 24 02:20 scgi_params
-rw-r--r-- 1 root root  636 Mar 24 02:24 scgi_params.default
-rw-r--r-- 1 root root  664 Mar 24 02:20 uwsgi_params
-rw-r--r-- 1 root root  664 Mar 24 02:24 uwsgi_params.default
-rw-r--r-- 1 root root 3610 Mar 24 02:24 win-utf
```

其中`fastcgi.conf`和`fastcgi_params`内容应该相同，用哪个都行

参考文献
> https://linuxize.com/post/how-to-install-php-on-ubuntu-18-04/
