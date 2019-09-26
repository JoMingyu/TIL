# Multiple select statements in Single query
SELECT를 comma로 묶어 SELECT하면 여러 select statement의 결과물을 한 번에 볼 수 있다.

```sql
SELECT
    (SELECT COUNT(*) FROM tbl_a) AS res_1,
    (SELECT AVG(some_value) FROM tbl_b) AS res_2,
    (SELECT SUM(some_value) FROM tbl_c) AS res_3
```
