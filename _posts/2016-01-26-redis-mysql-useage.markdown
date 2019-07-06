---
layout: post
title:  "Redis和Mysql安装使用"
date:   2016-01-26
categories: DB
author: 张翔
location: NanChang, China
cover: ""
description: Redis和Mysql安装以及使用
---
---
### Redis

安装redis
```shell
apt-get install redis-server
```
安装完成后，Redis服务器会自动启动，我们检查Redis服务器程序,执行
```shell
ps -aux|grep redis
```

通过启动命令检查Redis服务器状态,执行
```shell
netstat -nlt|grep 6379
```
安装Redis服务器，会自动地一起安装Redis命令行客户端程序。
在本机输入redis-cli命令就可以启动，客户端程序访问Redis服务器。执行：
```shell
redis-cli //redis-server启动redis服务
```

测试启动 
```shell
redis-cli ping 返回PONG，启动成功。
```
停止Redis，关闭服务
```shell
redis-cli shutdown
```

如果非默认端口，可指定端口:
```shell
redis-cli -p 6380 shutdown
```
init.d目录使用
```shell
/etc/init.d/redis-server stop
/etc/init.d/redis-server start
/etc/init.d/redis-server restart
```

redis设置远程连接：
```shell
redis.conf 文件在etc/redis/redis.conf
找到bind 127.0.0.1这行
修改为bind 0.0.0.0
允许所有外网可连
```

基本操作：
- ttl设置生存时间
- sadd添加数据
- sdiff集合的全部成员
- exits确定一个key是否存在
- smembers 返回key的全部成员
- del删除一个key
- expire设置key的过期时间
- select（0-15）选择数据库
- quit退出连接
- dbsize返回当前数据库中的key的数目
- info数据库的信息

设置密码
```xml
vi /etc/redis/redis.conf
requirepass pp123456
pkill redis #杀死进程
service redis start
auth pp123456
```

### MySQL
启动mysql服务
```shell
service mysql start
```
打开mysql数据库
```shell
mysql -u root -p
```
mysql打开远程访问权限

解决方法：修改/etc/mysql/my.cnf文件（/etc/mysql/mysql.conf.d/mysqld.cnf）。打开文件，找到下面内容：
```xml
# Instead of skip-networking the default is now to listen only on
# localhost which is more compatible and is not less secure.
bind-address  = 127.0.0.1
```
把上面这一行注释掉或者把127.0.0.1换成合适的IP，建议注释掉。

```sql
grant all on *.* to root@"%" identified by "密码";
flush privileges;
```


