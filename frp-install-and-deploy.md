---
title: frp的安装与部署（Ubuntu）
date: 2020-08-29 13:55:17
tags: [frp,ubuntu]
---
Frp是一个内外穿透工具。简单易用。网址：
> https://github.com/fatedier/frp

下面记录如何安装和部署为服务。以下在18.04版本测试通过

## 安装与测试

1. 根据系统版本，下载预编译好的二进制程序
 > https://github.com/fatedier/frp/releases

2. 根据教程修改配置文件，并测试。

## 部署为服务
### client一侧
3. 将软件包里的frpc拷贝到/usr/bin,并赋予执行权限
``` bash
cp ./frpc /usr/bin/
sudo chmod a+x /usr/bin/frpc
```

4. 将frpc.ini拷贝到/etc/frp/
``` bash
mkdir /etc/frp
cp ./frpc.ini /etc/frp/
```

5. 将systemd/frpc.service拷贝到/lib/systemd/system目录
``` bash
cp ./systemd/frpc.service /lib/systemd/system
```

6. 启用服务
``` bash
systemctl enable foo
```

### server一侧
同上，把frpc改为frps

## 运行与查看
7. 运行、停止
``` bash
sudo service frpc start
sudo service frpc stop
```

8. 查看状态
``` bash
systemctl status frpc
```

9. 查看日志文件
``` bash
tail /var/log/syslog
```
