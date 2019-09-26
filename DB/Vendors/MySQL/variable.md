# variable
SQL에서도 변수를 선언하면 좋은 경우가 있다. 어플리케이션 레벨보단 그냥 sql playground 단에서 니즈가 큼. 그냥 `SET @{name} = {value}` 하면 된다.

```sql
SET @start = 1, @finish = 10;
```

가져올 땐 `@{name}`으로.

```sql
SELECT @start;
SELECT * FROM places WHERE place BETWEEN @start AND @end;
```

## collation 관련 문제
이렇게 변수 선언해서 쓰다가 종종 아래같이 collation 관련 문제가 생길 때가 있다.

```
Illegal mix of collations (utf8mb4_unicode_ci,IMPLICIT) and (utf8mb4_general_ci,IMPLICIT) for operation '='
```

그럼 그냥 변수 선언에 collate 넣어주면 됨.

```sql
SET @rUsername = ‘aname’ COLLATE utf8mb4_unicode_ci;
```
