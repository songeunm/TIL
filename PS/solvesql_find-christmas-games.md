# [solvesql/SQLite] 크리스마스 게임 찾기

[Velog](https://velog.io/@semoon/solvesqlSQLite-크리스마스-게임-찾기)<br>
[문제](https://solvesql.com/problems/find-christmas-games/)

✔️ 난이도 1

## 문제 흐름
> games 테이블에서 게임 이름에 "Christmas" 또는 "Santa"가 포함된 게임을 찾는 쿼리를 작성해주세요.

`games` 테이블에서 `name`에 'Christmas'가 포함되거나, 'Santa'가 포함되는 레코드를 필터링해야한다.
특정 문자열 포함 여부 확인은 `LIKE`를 통해 수행했다.
"포함"이기 때문에 찾을 문자열 앞뒤로 와일드카드 `%`를 붙여줬다.

## 코드
```sql
SELECT
  game_id,
  name,
  year
FROM games
WHERE
  name LIKE '%Christmas%'
  OR name LIKE '%Santa%';
```

## 마무리
solvesql의 문제들도 거의 다 풀어가던 와중 solvesql에서 크리스마스 이벤트로 `Advent of SQL 2024`를 오픈한 것을 알게 되었다!
sql을 재밌게 공부하기 좋은 것 같다.
