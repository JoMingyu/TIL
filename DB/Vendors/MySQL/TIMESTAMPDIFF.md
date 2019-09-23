# TIMESTAMPDIFF
MySQL에서 Date나 DateTime 타입의 컬럼을 `-`연산하면 두 날짜 간 year가 다를 때 결과가 제대로 안 나온다. 나만 그런가;; 암튼 그렇던 그렇지 않던 별도로 지원되는 걸 쓰는 게 더 나아 보였다.

## DATEDIFF
더 간단한, 하위호환 함수는 `DATEDIFF`이다. `DATEDIFF(dt1, dt2)`에서, `dt1 - dt2`의 결과를 일 단위로 반환한다.

```sql
SELECT DATEDIFF('2018-03-08', '2018-03-01')
-> 7
```

## TIMESTAMPDIFF
DATEDIFF와 헛갈리게 참 `TIMESTAMPDIFF(unit, dt1, dt2)`에서 `dt2 - dt1`의 결과를 unit에 맞추어 반환한다.

```sql
SELECT TIMESTAMPDIFF(MONTH,'2003-02-01','2003-05-01');
-> 3
SELECT TIMESTAMPDIFF(YEAR,'2002-05-01','2001-01-01');
-> -1
SELECT TIMESTAMPDIFF(MINUTE,'2003-02-01','2003-05-01 12:05:55');
-> 128885
```
