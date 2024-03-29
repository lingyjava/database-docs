# 4·Redis主从复制

- [4·Redis主从复制](#4redis主从复制)
  - [Redis主从复制](#redis主从复制)
  - [哨兵模式](#哨兵模式)

## Redis主从复制
在实际开发中一般一台主服务器有多个从服务器，当主服务器发生故障时，从服务器能替代主服务器的功能，那么就要求主从服务器之间的数据同步，主服务器实时的把数据复制到从服务器上，一般主服务器复责写，从服务器负责读，实现读写分离。

## 哨兵模式
当主机发生故障时，从机中自动选出一个替代主机。
1. 在安装目录下创建sentinel.conf文件（哨兵配置文件）添加内容格式：`sentinel monitor myMonitor 127.0.0.1 6379 1`
2. 启动哨兵进入哨兵配置文件所在目录：redis-server sentinel.conf --sentinel。
3. 启动主机和从机。
4. 从机连接主机：`slaveof 127.0.0.1 6379`
5. 当从机down掉后，需要重新连接主机。
