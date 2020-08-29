---
title: Ubuntu18.04上，安装mysql的初始密码
date: 2020-03-24 22:37:17
tags: [ubuntu18.04,mysql]
---

mysql安装完以后，默认密码存放在`etc/mysql/debian.cnf`文件中

使用下面命令登入

``` bash
sudo mysql --defaults-file=/etc/mysql/debian.cnf
```

修改密码
``` bash
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'new-password';
```

flush
``` bash
FLUSH PRIVILEGES;
```

此外 `mysql_secure_installation` 也可以使用 `--defaults-file=/etc/mysql/debian.cnf`



参考文献：
> https://www.configserverfirewall.com/ubuntu-linux/reset-mysql-root-password-ubuntu/