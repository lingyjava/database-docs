# 3·MySQL常用功能

- [3·MySQL常用功能](#3mysql常用功能)
  - [event](#event)
  - [报价自动开始结束](#报价自动开始结束)
    - [计算时间差值函数](#计算时间差值函数)

## event
- [MySQL事件调度器event的使用](https://www.cnblogs.com/geaozhang/p/6821692.html)

## 报价自动开始结束
询价单自动开始、结束报价状态翻转，当询价单开始报价时间到达时自动将状态从未开始改为进行时，结束报价时间到达时自动将状态从进行中改为已结束。
```sql
-- 查询是否开启事件调度器
show variables like '%event_scheduler%';
-- 开启事件调度器
set global event_scheduler=1;

-- 开始报价事件
CREATE DEFINER=`scm_order_cgn`@`%` EVENT `quote_start_event` ON SCHEDULE EVERY 5 SECOND STARTS '2022-03-28 09:55:17' ON COMPLETION NOT PRESERVE ENABLE DO UPDATE inquiry_bill_info
SET inquiry_status = "UNDERWAY"
WHERE inquiry_status = "NOT_START"
  AND TIMESTAMPDIFF(SECOND,NOW(),start_quote_real_time) <= 0;
-- 结束报价事件
CREATE DEFINER=`scm_order_cgn`@`%` EVENT `quote_end_event` ON SCHEDULE EVERY 5 SECOND STARTS '2022-03-28 09:55:23' ON COMPLETION NOT PRESERVE ENABLE DO UPDATE inquiry_bill_info
SET inquiry_status = "ALREADY_OVER"
WHERE inquiry_status = "UNDERWAY"
  AND TIMESTAMPDIFF(SECOND,NOW(),end_quote_real_time) <= 0;

-- 查看存在的事件
select EVENT_NAME,LAST_EXECUTED from information_schema.EVENTS;

-- 删除事件
drop event quote_start_event;
drop event quote_end_event;
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
