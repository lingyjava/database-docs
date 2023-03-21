# [LeetCode1527.患某种疾病的患者](https://leetcode.cn/problems/patients-with-a-condition/)

```text
患者信息表：Patients

+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| patient_id   | int     |
| patient_name | varchar |
| conditions   | varchar |
+--------------+---------+
patient_id （患者 ID）是该表的主键。
'conditions' （疾病）包含 0 个或以上的疾病代码，以空格分隔。
这个表包含医院中患者的信息。
```

要求：写一条SQL 语句，查询患有 I 类糖尿病的患者ID （patient_id）、患者姓名（patient_name）以及其患有的所有疾病代码（conditions）。I 类糖尿病的代码总是包含前缀 DIAB1 。
按 任意顺序 返回结果表。

```text
输入：
Patients表：
+------------+--------------+--------------+
| patient_id | patient_name | conditions   |
+------------+--------------+--------------+
| 1          | Daniel       | YFEV COUGH   |
| 2          | Alice        |              |
| 3          | Bob          | DIAB100 MYOP |
| 4          | George       | ACNE DIAB100 |
| 5          | Alain        | DIAB201      |
+------------+--------------+--------------+
输出：
+------------+--------------+--------------+
| patient_id | patient_name | conditions   |
+------------+--------------+--------------+
| 3          | Bob          | DIAB100 MYOP |
| 4          | George       | ACNE DIAB100 | 
+------------+--------------+--------------+
解释：Bob 和 George 都患有代码以 DIAB1 开头的疾病。
```

## MySQL

### LIKE
```sql
SELECT patient_id, patient_name, conditions
FROM patients
WHERE conditions LIKE 'DIAB1%' OR conditions LIKE '% DIAB1%'
;
```

### REGEXP(正则)
```sql
SELECT patient_id, patient_name, conditions
FROM patients
WHERE conditions REGEXP('\\bDIAB1')
;
-- regex1 '\\bDIAB1'
-- regex2 ' DIAB1'
-- regex3 '\\bDIAB1.*\\b'
```

### 正则解释
\ 转义字符
\b 空格