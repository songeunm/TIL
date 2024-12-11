# [solvesql/SQLite] 최대값을 가진 행 찾기

[Velog](https://velog.io/@semoon/solvesqlSQLite-최대값을-가진-행-찾기)<br>
[문제](https://solvesql.com/problems/max-row/)

✔️ 난이도 2

## 문제 흐름
> points 테이블에서 가장 큰 x 값을 가지는 데이터와 가장 큰 y 값을 가지는 데이터의 id를 출력하는 쿼리를 작성해주세요. 결과는 오름차순으로 정렬해주세요.

x와 y에 각각 `max` 함수를 적용시켜야 한다.
각각 `id`와 `max` 함수 적용값을 서브쿼리로 구한 뒤 여기서 다시 `id`만 추출하고
이를 `UNION`을 통해 합쳐주는 식으로 진행했다.
그 다음 오름차순으로 정렬해주면 된다.

## 코드
```sql
SELECT id
FROM (SELECT id, max(x) FROM points)
union
SELECT id
FROM (SELECT id, max(y) FROM points)
ORDER BY 1;
```
