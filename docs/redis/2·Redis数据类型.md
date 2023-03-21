# 2·Redis数据类型

- [2·Redis数据类型](#2redis数据类型)
  - [String](#string)
  - [List](#list)
  - [Hash](#hash)
  - [Set](#set)
  - [sortedSet](#sortedset)

## String
字符串数据类型的相关命令用于管理 redis 字符串值。

相关命令：
| 命令                     | 作用                                                                    |
| ------------------------ | :---------------------------------------------------------------------- |
| APPEND key value         | 如果key存在，将value追加到原来值的末尾如果key不存在，执行set key value  |
| STRLEN key               | 返回key存储的字符串值的长度                                             |
| INCR                     | 自增                                                                    |
| DECR                     | 自减                                                                    |
| INCRBY                   | 加指定值                                                                |
| DECRBY                   | 减指定值                                                                |
| GETRANGE key start end   | 返回key中字符串值的子字符串，前后都闭合区间                             |
| SETRANGE key index value | 用value参数覆写给定key所存储的字符串值，从偏移量index开始               |
| SETEX key seconds value  | 将value关联到key，并将key的生存时间设为seconds秒，如果key已存在覆写旧值 |
| SETNX key value          | 将key的值设为value，仅当key不存在                                       |
| MSET                     | 设置多个                                                                |
| MGET                     | 获得多个                                                                |
| MSETNX                   | 设置多个当kty不存在时                                                   |
| GETSET                   | 将给定key的值设为value，并返回key的旧值                                 |

## List
列表是简单的字符串列表，按照插入顺序排序，可以添加一个元素到列表的头部或者尾部。数据可重复，类似于LinkedList链表。

相关命令：
| 命令                                 | 作用                                                              |
| ------------------------------------ | :---------------------------------------------------------------- |
| LPUSH                                | 左加入                                                            |
| RPUSH                                | 右加入                                                            |
| LRANGE key start stop                | 获得区间数据，0 -1代表取全部                                      |
| LPOP key                             | 移除并返回列表头部                                                |
| RPOP key                             | 移除并返回列表尾部                                                |
| LLEN key                             | 长度                                                              |
| LREM key count value                 | 根据参数count的值，移除列表中与参数value相等的元素                |
| LTRIM key start stop                 | 获取指定区间的数据赋值给key                                       |
| RPOPLPUSH k1 k2                      | 将第一个列表末尾元素拿出放到第二个列表头部                        |
| LSET key index value                 | 将列表key下标为index的元素值设置为value                           |
| LINSERT key BEFORE/AFTER pivot value | 将值value插入到列表key当中，位于pivot之前或之后，多个值默认第一个 |

## Hash
Hash 是一个 string 类型的 field（字段） 和 value（值） 的映射表，类似与Map集合，是一个键值对。适合用于存储对象。

相关命令：
| 命令                             | 作用                                          |
| -------------------------------- | :-------------------------------------------- |
| HSET                             | 插入                                          |
| HGET                             | 获得                                          |
| HMSET                            | 插入多个                                      |
| HMGET                            | 获得多个                                      |
| HGETALL                          | 获得键值                                      |
| HDEL                             | 删除                                          |
| HLEN key                         | 返回长度                                      |
| HEXISTS key field                | 查询哈希表key中，给定域field是否存在          |
| HKEYS                            | 获取哈希表中所有值                            |
| HVALS                            | 取出所有的域，取出所有的域的值                |
| HINCRBY key field INCREMENT      | 为哈希表key中的域field的值加上增量increment   |
| HINCRBYFLOAT key field INCREMENT | 为哈希表key中的域field加上浮点数增量increment |
| HSETNX                           | 将key的值设为value，仅当key不存在             |

## Set
Set 是 String 类型的无序集合。集合成员是唯一的，这就意味着集合中不能出现重复的数据。
集合对象的编码可以是 intset 或者 hashtable。
Redis 中集合是通过哈希表实现的，所以添加，删除，查找的复杂度都是 O(1)。

相关命令：
| 命令                                     | 作用                                                                    |
| ---------------------------------------- | :---------------------------------------------------------------------- |
| SADD key score1 member1 [score2 member2] | 将一个或多个member元素加入集合key中，已经存在与集合的member元素将被忽略 |
| SMEMBERS key                             | 返回集合key中的所有成员                                                 |
| SISMEMBER key member                     | 判断member元素是否集合key的成员                                         |
| SCARD key                                | 获取集合中元素的个数                                                    |
| SREM key value                           | 删除集合中元素                                                          |
| SRANDMEMBER key                          | 返回指定集合中的一个随机元素                                            |
| SPOP key                                 | 移除并返回集合中的一个随机元素                                          |
| SMOVE k1 k2 member                       | 将member元素从k1集合移动到k2集合                                        |
| SDIFF k1 [k2]...                         | 差集，在第一个集合中，不在后面任一集合中                                |
| SINTER k1 [k2]...                        | 交集                                                                    |
| SUNION k1 [k2]...                        | 并集                                                                    |

## sortedSet
在set集合的基础上，加一个score值。实现有序的set集合。有序集合和集合一样也是 string 类型元素的集合,且不允许重复的成员。不同的是每个元素都会关联一个 double 类型的分数。redis 正是通过分数来为集合中的成员进行从小到大的排序。有序集合的成员是唯一的,但分数(score)却可以重复。支持根据 Value 更新 score的， 也支持指定 Value 对 score 做增减，常用来做排行榜，热度榜。
集合是通过哈希表实现的，所以添加，删除，查找的复杂度都是 O(1)。 

相关命令：
| 命令                                     | 作用                                                                         |
| ---------------------------------------- | :--------------------------------------------------------------------------- |
| ZADD key score1 member1 [score2 member2] | 将一个或多个member元素及其score值加入到有序集key中，或者更新已存在成员的分数 |
| ZREM key value                           | 移除key集合中的value值                                                       |
| ZCARD                                    | 返回元素个数                                                                 |
| ZCOUNT key min max                       | 返回scores在min和max之间元素个数                                             |
| ZRANK key value                          | 返回有序集key中成员value的排名。其中有序集成员按score值递增顺序排列          |
| ZRANGE key index [withscores]            | 返回区间中的数据，score从小到大（显示score）                                 |
| ZREVRANGE key index [withscores]         | 返回区间中的数据，score从大到小（显示score）                                 |
