# 3·MySQL事件

- [3·MySQL事件](#3mysql事件)
  - [event](#event)
  - [使用案例](#使用案例)
    - [计算时间差值函数](#计算时间差值函数)

## event
- [MySQL事件调度器event的使用](https://www.cnblogs.com/geaozhang/p/6821692.html)

## 使用案例
状态翻转，当开始时间或结束时间到达时自动修改状态。
```sql
-- 查询是否开启事件调度器
show variables like '%event_scheduler%';
-- 开启事件调度器
set global event_scheduler=1;

-- 创建事件
CREATE DEFINER=`[库名]`@`%` EVENT `[事件名]` ON SCHEDULE EVERY 5 SECOND STARTS '[开始执行时间]' ON COMPLETION NOT PRESERVE ENABLE DO 
[具体需要执行的 update 语句];

-- 查看存在的事件
select EVENT_NAME,LAST_EXECUTED from information_schema.EVENTS;

-- 删除事件
drop event [事件名];
```
**存在的问题：**

1. 数据库服务器时间、时区导致的时间不一致。
2. 影响数据库性能，可能锁表。

### 计算时间差值函数
`TIMESTAMPDIFF(unit,begin,end)`
返回begin-end的整数结果 ，单位为unit
有效单位：

- `MICROSECOND`
- `SECOND`
- `MINUTE`
- `HOUR`
- `DAY`
- `WEEK`
- `MONTH`
- `QUARTER`
- `YEAR`
