# Query kill하는 방법
query time이 길어지면 그만큼 lock 시간도 길어지는 것. query time이 긴 쿼리 혹은 query문 작성이 잘못되어 너무 많은 시간을 소비하는 경우, client에서 query stop을 하더라도 서버는 쿼리를 계속 실행하고 있을 수 있음. 다음 구문을 통해 kill 가능

```sql
show processlist;
kill {process_id};
```
