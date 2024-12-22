# [solvesql/SQLite] 미세먼지 수치의 계절간 차이

[Velog](https://velog.io/@semoon/solvesqlSQLite-미세먼지-수치의-계절간-차이)

[문제](https://solvesql.com/problems/finedust-seasonal-summary/)

✔️ 난이도 3

## 문제 흐름
> 3월 1일부터 5월 31일까지를 ‘spring’, 6월 1일부터 8월 31일까지를 ‘summer’, 9월 1일부터 11월 30일까지를 ‘autumn’, 나머지를 ‘winter’라고 이름 붙이고 각 계절별로 미세먼지 농도의 중앙값과 평균을 계산하는 쿼리를 작성해주세요.
쿼리 결과는 아래 세 컬럼을 포함해야 하고, PM10 농도의 평균값은 소수점 셋째 자리에서 반올림 해 소수점 둘째 자리까지 표시되어야 합니다.
- season: 계절 (’spring’, ‘summer’, ‘autumn’, ‘winter’)
- pm10_median: PM10 농도의 중앙값
- pm10_average: PM10 농도의 평균

`substr`을 통해 `measured_at`에서 월을 추출하고,
`CASE`문을 통해서 각 월을 해당하는 계절로 변환했다.
중앙값은 `median` 함수를 통해 집계할 수 있고,
평균은 `avg` 함수를 통해 집계할 수 있다.
평균값은 `round`함수를 통해 반올림하여 소수점 둘째자리까지 나타내도록 했다.

## 코드
```sql
SELECT
  CASE
    WHEN substr(measured_at, 6, 2) IN ('03', '04', '05') THEN 'spring'
    WHEN substr(measured_at, 6, 2) IN ('06', '07', '08') THEN 'summer'
    WHEN substr(measured_at, 6, 2) IN ('09', '10', '11') THEN 'autumn'
    ELSE 'winter'
  END season,
  median(pm10) pm10_median,
  round(avg(pm10), 2) pm10_average
FROM measurements
GROUP BY 1;
```
