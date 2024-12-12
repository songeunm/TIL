# [solvesql/SQLite] 3년간 들어온 소장품 집계하기

[Velog](https://velog.io/@semoon/solvesqlSQLite-3년간-들어온-소장품-집계하기)<br>
[문제](https://solvesql.com/problems/summary-of-artworks-in-3-years/)

✔️ 난이도 2

## 문제 흐름
> 아래와 예시와 같은 형태로 각 분류에 대해 연도별 추가된 소장품 수를 집계하는 쿼리를 작성해주세요.
<img src=https://velog.velcdn.com/images/semoon/post/04f74059-ef62-4a60-8ea7-d6ef5942220d/image.png width=40%>
각 행은 분류(classification) 컬럼 기준으로 오름차순 정렬되어 있어야 합니다.
또한, 집계하는 3년간 추가된 특정 분류의 소장품이 없더라도 해당 분류와 집계 내역을 결과 테이블에서 누락시키지 말고 포함해주세요.

이전에 한 번 풀어본 유형의 문제이다!
`CASE`문을 통해 집계하는 방법과 `pivot` 함수를 통해 집계하는 방법이 있다.
SQLite는 `pivot`함수를 지원하지 않기 때문에 `CASE`문을 이용해 해결했다.
때문에 실행해서 결과를 확인해보지는 못했지만 `pivot`을 사용한 방법도 함께 아래 코드에 올려놓았다!

`substr` 함수를 통해 소장 일시 `acquition_date`에서 1번째부터 4문자를 뽑아 연도를 추출한다.
`CASE`문을 통해 각 2014, 2015, 2016 연도일 경우 1, 아닌 경우 0을 반환하도록 한 뒤 그 합계를 통해 집계했다.
그룹화와 정렬 기준은 1번 컬럼 `classification`으로 설정했다.


## 코드
```sql
-- solution 1
SELECT
  classification,
  sum(CASE WHEN substr(acquisition_date, 1, 4) = '2014' THEN 1 ELSE 0 END) AS '2014',
  sum(CASE WHEN substr(acquisition_date, 1, 4) = '2015' THEN 1 ELSE 0 END) AS '2015',
  sum(CASE WHEN substr(acquisition_date, 1, 4) = '2016' THEN 1 ELSE 0 END) AS '2016'
FROM artworks
GROUP BY 1
ORDER BY 1;

-- solution 2
SELECT
  classification, '2014', '2015', '2016'
FROM artworks AS base_table
pivot(
  count(substr(acquisition_date, 1, 4))
  FOR substr(acquisition_date, 1, 4) IN ('2014', '2015', '2016')
) AS pivot_table;
```
