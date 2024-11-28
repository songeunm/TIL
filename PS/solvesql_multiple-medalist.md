# [solvesql/SQLite]복수 국적 메달 수상한 선수 찾기

[Velog](https://velog.io/@semoon/solvesqlSQLite복수-국적-메달-수상한-선수-찾기)<br>
[문제](https://solvesql.com/problems/multiple-medalist/)

✔️ 난이도 3

## 문제 흐름
> 2000년 이후의 메달 수상 기록만 고려했을 때,

➡️ 조건
`record` 테이블을 베이스로 잡는다.
메달 수상 기록 연도는 `games` 테이블의 `year` 컬럼에 있다.
`year`가 2000 이상이면서, `medal`이 `NULL`이 아닌 레코드를 `WHERE`를 이용해서 필터링해야한다.

> 메달을 수상한 올림픽 참가 선수 중 2개 이상의 국적으로 메달을 수상한 기록이 있는 선수의 이름을 조회하는 쿼리를 작성해주세요.

➡️ 추출 내용
출력하고자 하는 컬럼은 선수의 이름. 선수의 이름은 `athletes` 테이블의 `name`컬럼에 있다.
또 국적은 `teams` 테이블의 `team` 컬럼에 있다.
조건과 추출을 위한 컬럼들이 모두 흩어져있기 때문에 베이스 테이블에 필요 테이블들을 `LEFT JOIN`을 이용해서 전부 붙여줬다.
이후 조건에 맞추어 필터링을 거친 후
선수별 국적의 수를 카운트하여 1 초과 (2 이상)인 경우를 필터링한다.
여기서 국적의 수는 중복을 제거하여 카운트한다.

> 조회된 선수의 이름은 오름차순으로 정렬되어 있어야 합니다.

➡️ 정렬
이름을 기준으로 오름차순 정렬을 해준다.

## 코드
```sql
SELECT name
FROM records r
LEFT JOIN games g ON r.game_id = g.id
LEFT JOIN teams t ON r.team_id = t.id
LEFT JOIN athletes a ON r.athlete_id = a.id
WHERE
  year >= 2000
  AND medal IS NOT NULL
GROUP BY athlete_id
HAVING count(DISTINCT team) > 1
ORDER BY 1;
```
