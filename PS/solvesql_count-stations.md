# [solvesql/SQLite] 지자체별 따릉이 정류소 개수 세기

[Velog](https://velog.io/@semoon/solvesqlSQLite-지자체별-따릉이-정류소-개수-세기)<br>
[문제](https://solvesql.com/problems/count-stations/)

✔️ 난이도 1

## 문제 흐름
> station 테이블을 활용해 소속 지자체별 따릉이 정류소의 개수를 집계하는 쿼리를 작성해주세요. 쿼리 결과에는 아래 2개의 컬럼이 있어야 합니다.

`station` 테이블을 베이스 테이블로 잡고,
소속 지자체 `local`을 기준으로 그룹화하여
`station_id`의 개수를 중복을 제거하여 카운트하고, `local`과 함께 출력한다.
마지막으로 집계값을 기준으로 오름차순으로 정렬한다.

## 코드
```sql
SELECT local, count(DISTINCT station_id) num_stations
FROM station
GROUP BY local
ORDER BY 2;
```
