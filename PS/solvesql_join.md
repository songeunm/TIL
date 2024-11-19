# [solvesql/SQLite] 두 테이블 결합하기

[Velog](https://velog.io/@semoon/solvesqlSQLite-두-테이블-결합하기)<br>
[문제](https://solvesql.com/problems/join/)

✔️ 난이도 2

## 문제 흐름
베이스 테이블은 선수의 정보가 들어있는 `records`로 잡았다.
선수 정보에 참가한 경기 이름을 매칭시키기 위해 참여 경기 id를 key로 `records`테이블과 left join을 했다.
그렇게 경기 이름을 "Golf"로 조건을 걸어준 뒤 `athlete_id`를 중복을 제거하여 출력한다.

## 코드
```sql
SELECT DISTINCT athlete_id
FROM records r
LEFT JOIN events e ON e.id = r.event_id
WHERE sport = "Golf";
```
