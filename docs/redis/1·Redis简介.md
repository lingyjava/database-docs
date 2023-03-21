# 1·Redis简介

- [1·Redis简介](#1redis简介)
  - [Redis是什么](#redis是什么)
  - [特性](#特性)
  - [配置Redis](#配置redis)
  - [key](#key)

## Redis是什么
Redis是一个基于缓存的、Key-Value形式的非关系型数据库。
- [菜鸟教程-Redis](https://www.runoob.com/redis/redis-tutorial.html)

## 特性
Redis特性：
1. redis默认16个数据库，下标从零开始，初始默认使用零号库。
2. select命令切换数据库，select 3：切换到下标为3的库。
3. dbsize命令，查看打那个前数据库的key数量。
4. flushdb命令，清空当前库。
5. Flushall命令，清空全部库。
6. redis默认端口号为6379。
7. redis默认不设置密码，将安全维护交给操作系统，只能能进入操作系统就能使用redis数据库。
8. redis中关于range范围，都是双闭合区间。

## 配置Redis
1. 官网安装，约10M。
2. cmd进入安装目录，将server安装成服务redis6379。
    执行命令：
    `Redis-server --service-install redis.windows.conf --service-name redis6379`

3. 启动服务：net start redis6379。
4. 关闭服务：net stop redis6379。
5. 连接reids：`Redis-cli -p 6379`
6. 使cmd显示中文：`redis-cli --raw -p 6379`
7. 卸载服务，`redis-server --service-uninstall --service-name redis6379`

## key
key 命令用于管理 redis 的键。

相关命令：
| 命令               | 作用                                         |
| ------------------ | :------------------------------------------- |
| KEYS *             | 查询该库中所有key                            |
| SCAN               | 循环查询键，-0：已查完                       |
| EXISTS key         | 判断某个key是否存在，-0：不存在              |
| EXPIRE key seconds | 给key设置过期时间seconds秒                   |
| TTL key            | 查看还有多少秒过期，-1：永不过期，-2：已过期 |
| TYPE key           | 查看key的类型                                |
| SET key value      | 设置值                                       |
| DEL key            | 删除                                         |
| GET key            | 获取指定key的value                           |

