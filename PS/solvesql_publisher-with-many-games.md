# [solvesql/SQLite] 게임을 10개 이상 발매한 퍼블리셔 찾기

[Velog](https://velog.io/@semoon/solvesqlSQLite-게임을-10개-이상-발매한-퍼블리셔-찾기)<br>
[문제](https://solvesql.com/problems/publisher-with-many-games/)

✔️ 난이도 2

## 문제 흐름
> 데이터베이스를 조회해 퍼블리셔로 참여한 게임이 10개 이상인 회사의 이름을 출력하는 쿼리를 작성해주세요.

게임의 목록은 `games` 테이블에 있고 이 테이블에는 게임 id, 퍼블리셔 id 등이 포함되어있다.
하지만 출력해야하는 컬럼은 회사의 이름인데, 이는 `companies` 테이블에 있다.
두 테이블은 `games.publisher_id`와 `companies.company_id`를 키로 join할 수 있다.

solution 1은 join을 먼저 진행한 뒤 조건을 필터링하였고,
solution 2는 조건을 필터링한 뒤 join을 진행했다.
아무래도 solution 2처럼 필터링 후 join을 진행하게될 시 처리해야할 레코드가 적어지기 때문에 더 효율적일 것이라고 생각된다.

## 코드
```sql
-- solution 1
SELECT c.name
FROM games g
LEFT JOIN companies c ON g.publisher_id = c.company_id
GROUP BY publisher_id
HAVING count(DISTINCT game_id) >= 10;

-- solution 2
SELECT name
FROM companies c
JOIN (
  SELECT publisher_id
  FROM games
  GROUP BY publisher_id
  HAVING count(DISTINCT game_id) >= 10
) g
ON c.company_id = g.publisher_id;
```
